# News-Recommendation-System
We propose a news recommendation approach based on articles similarity and person co-occurencies found in the same articles. 
The potential business profit is in article by article reading. It helps to increase user active time at service. 

Direct benefit - more time - more advertisment for example.

We store dataset in [data](https://github.com/rayonnant14/News-Recommendation-System/tree/main/data) folder. Dataset creating, entity linking system and graph building, recommendation algorithm implementation can be found in [ria_dataset.ipynb](https://github.com/rayonnant14/News-Recommendation-System/blob/main/research/ria_dataset.ipynb) and [social_network_project.ipynb](https://github.com/rayonnant14/News-Recommendation-System/blob/main/research/social_network_project.ipynb) respectively. 

Contributors:
- Artem Aroslankin
- Polina Smolnikova

### Dataset
Ria News dataset contains last news from several categories:
- politics
- economy
- science
- culture
- incidents

### Entity Linking system with [DeepPavlov](https://github.com/deepmipt/DeepPavlov)
Entity Linking component performs the following steps:

- the substring, detected with NER (Russian), is fed to TfidfVectorizer and the resulting sparse vector is converted to dense one

- Faiss library is used to find k nearest neighbours for tf-idf vector in the matrix where rows correspond to tf-idf vectors of words in entity titles

- entities are ranked by number of relations in Wikidata (number of outgoing edges of nodes in the knowledge graph)

- BERT (Russian) is used for entities ranking by entity description and by sentence that mentions the entity

### Recommendation Algorithm
Out recommendation scenario is complementary type: for an article suggest list of complementary articles.   
Generally, algorithm consists of two parts:
1. Find TopK similar articles.
2. Reranking articles from 1. with information about article importance.

#### Find TopK similar articles
Firstly, articles are preprocessed:
* Removal of 1 offer with information about the date and place of the event
* Deleting a sentence with information that is not related to the news (for example, "only selected quotes in our telegram channel")
* Downcast
* Remove punctuation marks
* Tokenization, stopword removal and lemmatization
* Delete words less than 3 characters long

Next step, Word2Vec model is used for articles encoding and Word Mover’s Distance as a subfunction of similarity measure.
And as a final list, topK by similarity measure is taken as a first level of recommendation list.

#### Reranking articles
At this step, topK list of related articles is available.
*How to predict which articles are significant to read, and which are not*?

Our own proposed algorithm is the following:
1. Build the graph containing person co-occurencies in articles. 
2. Normalize weights.
3. Compute PageRank scores for each vertex.
4. Compute importance score for each article by sum of PR scores of any person mentioned in the article.
5. Rerank TopK list by importance score.

Examples:
1. Local politics news
Article currently opened - 'Депутат госдумы обязан присутствовать во время пленарных заседаний палаты...'
Top from suggestion list - 'Президент России Владимир Путин представил нового главу мчс Александра Куренкова...'  
2. Space news
Article currently opened -  'Александр скворцов на счету которого три космических полета уходит из российского отряда космонавтов...'
Top from suggestion list - 'Капсула космического корабля crew dragon с экипажем из четырех человек приводнилась в атлантическом океане...'

### Quality assessment 
There are no open-source real user's eventstreams related to news products (No real answers for algorithm to check).
The way of quality assurance is expert assessment (human eye).
Algorithm of assessment:
1. Find the most interesting article to start with.
2. Take 3 articles from top of list of recommendations.
3. Use MAP@3 to evualute.

The result is ambigous, one person collected high values, while another one has extremely low results.

Another algorithm of assessment is about article by article scrolling.
1. Take 10 interested articles.
2. If interesting in top3. use next.
3. Repeat until no interesting in top3.
4. Count how many articles were scrolled.

Here, we have in average about 8 articles length of session. It is a great baseline to start with.
