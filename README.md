# News-Recommendation-System

Contributors:
- Artem Aroslankin
- Polina Smolnikova

### Idea

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
Top from suggestion list - 'Президент России Владимир Путин представил нового главу мчс александра куренкова'  
2. Space news
Article currently opened -  "Александр скворцов на счету которого три космических полета уходит из российского отряда космонавтов..."

Top from suggestion list - 'Капсула космического корабля crew dragon с экипажем из четырех человек приводнилась в атлантическом океане...'


