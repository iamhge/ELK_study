# Elastic Search

## 1. Elastic Search와 관계형 DB
### 1.1. ES의 data flow
ex) document 1, 2, 3에 따라 name과 professor가 채워져있다.  
1. document들을 Text에 따라 매칭시켜, indexing한다.  
2. 저장한 index를 사용자가 search한다.
![ES_dataflow](./elk_image/ES_dataflow.PNG)  
<elasticsearch의 data flow>  

### 1.2. 저장방식의 차이
![ES_es_index](./elk_image/ES_es_index.PNG)  
<ES의 저장방식에 따른 index>

![ES_rdb_table](./elk_image/ES_rdb_table.PNG)  
<관계형 데이터 베이스의 저장방식에 따른 database>

#### ES
* 키워드를 저장하여, 키워드에 따른 document의 검색이 가능하다.  
* 방식이 해시 테이블 자료구조와 비슷하여, Big-O notation 관점에서 보면 order 1의 복잡도를 가진다.

#### RDB(관계형 데이터 베이스)
* document 각각을 모두 순회하며, 원하는 키워드를 검색한다.  
* search과정에서 document 1~n까지 모두 순회해야 하므로 order n 의 복잡도를 가지게 된다.  

so, 위와 같은 검색에서는 ES의 search가 매우 빠르게 된다.  
but, 데이터가 방대해짐에 따라 키워드가 많아지면 ES의 search 속도가 느려지게 되며, 관계형 DB를 사용함이 나을 수 있다.  

![ES_es_vs_rdb_table](./elk_image/ES_es_vs_rdb_table.PNG)  
<용어 차이>  

![ES_es_vs_rdb_table2](./elk_image/ES_es_vs_rdb_table2.PNG)  
<명령어 차이>  

