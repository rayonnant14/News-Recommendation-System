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
