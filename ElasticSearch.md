# Elastic Search

## 1. Elastic Search와 관계형 DB
### 1.1. ES의 data flow
ex) document 1, 2, 3에 따라 name과 professor가 채워져있다.  
1. document들을 Text에 따라 매칭시켜, indexing한다.  
2. 저장한 index를 사용자가 search한다.  
![ES_dataflow](./elk_image/ES_dataflow.PNG)  
<elasticsearch의 data flow>  

### 1.2. 저장방식의 차이
|Text|Document|
|---|---|
|John|doc1, doc2|
|Database|doc1, doc3|
|Tom|doc3|
|Algorithm|doc2|

<ES의 저장방식에 따른 index>  

|Documnet|context|
|---|---|
|doc1|"class":{  "name":"database"  "professor":"John"  }|
|doc2|"class":{  "name":"algorithm"  "professor":"John"  }|
|doc3|"class":{  "name":"database"  "professor":"Tom"  }|

<관계형 데이터 베이스의 저장방식에 따른 database>

#### ES
* 키워드를 저장하여, 키워드에 따른 document의 검색이 가능하다.  
* 방식이 해시 테이블 자료구조와 비슷하여, Big-O notation 관점에서 보면 order 1의 복잡도를 가진다.

#### RDB(관계형 데이터 베이스)
* document 각각을 모두 순회하며, 원하는 키워드를 검색한다.  
* search과정에서 document 1~n까지 모두 순회해야 하므로 order n 의 복잡도를 가지게 된다.  

so, 위와 같은 검색에서는 ES의 search가 매우 빠르게 된다.  
but, 데이터가 방대해짐에 따라 키워드가 많아지면 ES의 search 속도가 느려지게 되며, 관계형 DB를 사용함이 나을 수 있다.  

|Elastic Search|Relational DB|
|---|---|
|Index|Database|
|Type|Table|
|Document|Row|
|Field|Column|
|Mapping|Schema|
|Shard|Physical partition|
|Route|Logical partition|
|Parent/child, nested|Relational|
|Query dsl|sql|

<용어 차이>  

|Elastic Search|Relational DB|
|---|---|
|GET|Select|
|PUT|Update|
|POST|Insert|
|DELETE|Delete|

<데이터 접근  차이>  

## 2. 데이터 입력, 조회, 삭제
### 2.1. 데이터 처리
* ES는 REST API를 사용한다.
* ES의 데이터 저장 형식은 json document이다.
* 저장 뿐아니라 쿼리, 클러스터 설정 등 모든 정보를 json형태로 주고받는다.
#### REST API 
* HTTP 기반으로 필요한 자원에 접근하는 방식을 정해놓은 아키텍쳐  
* 자원별로 고유 URL로 접근이 가능  
* http 메서드 GET, PUT, POST, DELETE를 이용해서 자원을 처리  
* 이러한 특성을 가진 시스템을 RESTful한 시스템이라 말한다.  

ex) RESTful한 시스템

    PUT https://user.com/iamhge -d {"name":"IHG", "age":22, "gender":"f"}
    GET https://user.com/iamhge
    DELETE https://user.com/iamhge

|Elastic Search|Relational DB|CRUD|
|---|---|---|
|GET|Select|Read|
|PUT|Update|Update|
|POST|Insert|Create|
|DELETE|Delete|Delete|

<ES, RDB, CRUD 비교>  


### 2.2. index와 document

#### 2.2.1. index
index 조회

    $ curl -XGET http://localhost:9200/<index명>?pretty
    $ curl -XGET http://localhost:9200/?pretty

* curl : ubuntu에서 REST API 보내기 위해 curl 커멘드 사용   
* -X : Prefix   
* GET : data 읽음   
* ?pretty : 결과값을 깔끔하게 확인하기 위한 명령 (json type)   
* 삭제된 index, 없던 index의 document에 대해 조회한 경우 "type":"index_not_found_exception", "status":404가 return된다.

index 생성

    $ curl -XPUT http://localhost:9200/<index명>?pretty
    $ curl -XPUT http://localhost:9200/classes?pretty
    
* true가 뜨면 index 생성됐다는 의미

index 삭제

    $ curl -XDELETE http://localhost:9200/<삭제할 index명>?pretty
    $ curl -XDELETE http://localhost:9200/classes?pretty
    
* 정상적으로 삭제되면, "acknowledged":true 응답이 return된다.
* 이후 index에 대해 GET 명령어를 쓰면 "found" : false 응답을 받는다.

#### 2.2.2. document
* ES에서는 단일 document 별로 고유한 URL을 갖는다.
* document에 접근하는 URL은 다음과 같다.
```
http://<host>:<port>/<index>/<doc_type>/<doc_id>
```
* 위 version은 ES 6.x 이전까지의 구조였다.
* ES 7.0 부터는 document type 개념이 사라져, <document type>위치에 고정자 _doc으로 접근한다.  
* 아래 예제들은 6.x 이전의 버전기준으로 작성하였다.

document 입력

    $ curl -XPUT http://localhost:9200/<index명>/<doc_type>/<doc_id> -d 
    { 
        "name":"Haegyeion Im",
        "message":"pigonada"
    }

* 처음으로 document를 입력시, "result":"created"로 표시된다.

실수로 기존의 document가 덮어씨워지는 것을 방지하기 위해서는 입력 명령에 \_doc대신 \_create를 사용해서 새로운 document의 입력만 허용하는 것이 가능하다. 

    $ curl -XPUT http://<host>:<port>/<index>/<doc_type>/<doc_id>/\_create

+) 7.0 버전 이상 : $ curl -XPUT http://<host:<port>/<index>/\_create/<doc_id>

document 조회

    $ curl -XGET http://localhost:9200/<index명>/<doc_type>/<id>

documnet 삭제

    $ curl -XDELETE http://localhost:9200/<index명>/<doc_type>/<id>

* "result":"deleted"결과가 return 된다.
* document는 삭제됐지만 index는 남아있는 경우, document에 대해 GET을 하면 "found":false 응답을 받는다.

document 생성

    $ curl -XPOST http://localhost:9200/<index명>/<type명>/<id>/ -d '{ : }'
    $ curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"Algorithm", "professor":"John"}'

* index가 없어도 생성이 가능하다.
* index명과 type명을 명시해주면 document 생성 가능
* document id를 입력하지 않으면 자동생성된다. (PUT 매서드는 동작X)

파일에 저장된 document 생성

    $ curl -XPOST http://localhost:9200/<index명>/<type명>/{id}/ -d @<파일명>
    $ curl -XPOST http://localhost:9200/classes/class/1/ -d @oneclass.json

### 2.3. data update
* 입력한 document를 수정하기 위해서는 기존 document의 URL에 변경될 내용의 document 내용을 다시 PUT하여 대치가 가능하다. 
* 하지만 필드가 많은 document에서 필드 하나만 바꾸기 위해 전체 document 내용을 매번 다시 입력해야한다.

    $ curl -XPOST http://<host>:<port>/<index>/<doc_type>/<doc_id>/\_update

+) 7.0버전 이상 : $ curl -XPOST http://<host>:<port>/<index>/_update/<doc_id>

document update  

    $ curl -XPOST http://localhost:9200/<index명>/<type명>/<id>/\_update -d '{"doc":"<update할 document명>":<update할 data>}}'
    $ curl -XPOST http://localhost:9200/classes/class/1/\_update -d '{"doc":{"unit":1}}' // 1학점임을 추가함

document 수정

    $ curl -XPOST http://localhost:9200/<index명>/<type명>/<id>/\_update -d '{"doc":"<수정할 document명>":<수정할 data>}}'
    $ curl -XPOST http://localhost:9200/classes/class/1/\_update -d '{"doc":{"unit":1}}' // 2학점이라 수정

* 동일한 URL에 다른 내용의 document를 다시 입력하게 되면 기존 document는 삭제되고 새로운 document로 덮어씌워진다.
* 이때 result에는 updated가 표시된다.
* update 후 "\_version":n+1로 버전이 증가한다.

script를 사용해 값을 변경

    $ curl -XPOST http://localhost:9200/classes/class/1/\_update -d '{"script":"ctx.\_source.unit += 5"}' // 2학점에서 5학점을 올려 7학점으로 수정

### 2.4. bulk
\_bulk API
* 여러 명령을 배치로 수행할 수 있다.
* \_bulk의 명령문과 데이터문은 반드시 한 줄 안에 입력이 되어야 하며 줄바꿈을 허용하지 않는다. 

파일을 통해 여러개의 document를 한번에 ES에 삽입

    $ curl -XPOST http://localhost:9200/\_bulk?pretty --data-binary @<파일명>
    $ curl -XPOST http://localhost:9200/\_bulk?pretty --data-binary @classes.json

* --data-binary : 파일으로부터 document 삽입  

### 2.5. Mapping(RDB의 schema)

Q. mapping 없이 ES에 data를 넣을 수 있는가?  
A. 할 수 있다. 하지만 mapping없이 data를 넣는 것은 상당히 위험한 일.  
ex) 숫자 정보를 입력하였지만, ES는 문자로 인식할 수 있다.  

* 잘못지정된 type 때문에 kibana 등에서 잘못된 visualization 가능.
* so, data를 관리할 때는 mapping을 추가해야한다.

mapping 채우기

    $ curl -XPUT 'http://localhost:9200/<index명>/<type명>/\_mapping' -d @<파일명>
    $ curl -XPUT 'http://localhost:9200/classes/class/\_mapping' -d @classesRating_mapping.json
   
* mapping 완료 후 document를 삽입한다.

### 2.6. Search
* search는 index 단위로 이루어진다.
* 쿼리를 입력하지 않는 경우는 전체 document를 찾는 match_all 검색을 한다.
* ES는 데이터를 term으로 분석 과정을 거쳐 저장하기 때문에 검색 시 대소문자, 단수나 복수, 원형 여부와 상관 없이 검색이 가능하다. 이러한 특징을 Full Text Search(전문 검색)라고 한다.


search

    $ curl -XGET localhost:9200/<index명>/<type명>\_search?pretty
    $ curl -XGET localhost:9200/basketball/record\_search?pretty

URI search

    $ curl -XGET localhost:9200/<index명>/<type명>\_search?q=<field명>:<검색어>&pretty
    $ curl -XGET localhost:9200/<index명>/<type명>\_search?q=<value>&pretty
    $ curl -XGET 'localhost:9200/basketball/record\_search?q=points:30&pretty' // query는 points가 30인 것만 보여줘라

* q : query
* URI 쿼리에서는 AND, OR, NOT 의 사용이 가능하다.
ex) $ curl -XGET 'localhost:9200/basketball/record\_search?q=points:30 AND points:20'  

+) 7.0 버전 이상 : $ curl -XGET <host>:<port>/<index>/\_search?q=value  

request body(data body) search

    $ curl -XGET 'localhost:9200/<index명>/<type명>\_search -d'
    {
        "query":{
            " ":{" ": }
        }
    }
    $ curl -XGET 'localhost:9200/basketball/record\_search -d'
    {
        "query":{
            "term":{"points":30}
        }
    }
    
* data body search는 검색 쿼리를 데이터 본문으로 입력하는 방식이다.
    
+) request body에는 여러가지 옵션이 있다.  

### 2.7. Aggregation
aggregation
* 엘라스틱서치 안에 있는 document의 조합을 통해 어떠한 값을 도출할 때 쓰이는 방법

#### 2.7.1. Metric Aggregation
metric aggregation
* 산술할 때 쓰임 ex) 평균, 최댓값, 최솟값 구할 때

aggregation format

    "aggregations":{
        "<aggregation_name>":{
            "<aggregation_type>":{
                <aggregation_body>
            }
            [, "meta":{ [<meta_data_body>] } ]?
            [, "aggregations":{ [<sub_aggregation>]+ } ]?
        }
        [, "<aggregation_name_2>":{ ... } ]*
    }
    
ex) points의 평균값 구하기

    {
        "size":0, // 여러개의 정보가 도출되는 대신, aggregation 정보만 보기위해 size = 0으로
        "aggs":{  // aggregations
            "avg_score":{  // aggregation name
                "avg":{  // aggregation type
                    "field":"points"  // aggregation body
                }
            }
        }
    }
   
aggregation search 명령어

    $ curl -XGET localhost:9200/\_search?pretty --data-binary @<파일명>

* aggs = aggregations  
* avg : 평균값을 구할 것이다.  

* 원하는 결과값을 구하기 위해 <aggregation_type> 필드를 변경해본다.   
* ex) max, min, sum, ..  

* stats를 aggregation_name으로 사용하면 aggregation으로 구했던 모든 값들이 함께 출력된다.
#### 2.7.2. Bucket Aggregation

bucket aggregation
* group by : document들을 group지어준다.  

ex) team별로 document를 묶기 (terms_aggs.json파일)

    {
        "size":0, // 여러개의 정보가 도출되는 대신, aggregation 정보만 보기위해 size = 0으로
        "aggs":{  // aggregations
            "players":{  // aggregation name
                "terms":{  // aggregation type
                    "field":"team"  // aggregation body
                }
            }
        }
    }
