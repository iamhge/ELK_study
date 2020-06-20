## 1. ELK Stack
로그를 한 곳으로 집중시키고 분석하고 적당한 쿼리를 만들어 원하는 데이터를 도출 해낼 수 있는 로그 관리 솔루션

사용자가 서버나 DB로부터 원하는 데이터를 실시간으로 수집하고 검색, 분석하여 시각화 시키는 오픈소스 서비스
### 1.1. ELK
#### E (Elastic Search)
Apache Lucene 기반의 Java 오픈소스 분산검색 엔진

분산형 RESTful 검색 및 분석 엔진. 데이터를 중심부에 저장하여 예상되는 항목을 검색하고 , 예상치 못한 항목을 밝혀 낼 수 있다.
> 1. 정형, 비정형, 위치정보, 메트릭 등 원하는 방법으로 다양한 유형의 검색을 수행하고 결합할 수 있다.
> 2. 표준 RESTful API와 JSON 사용해 데이터 처리
> 3. 대량의 데이터를 실시간으로 신속하게 저장, 검색 및 분석할 수 있다.
#### L (Logstash)
오픈소스 서버측 데이터 처리 및 수집 파이프라인 도구

각 데이터 베이스의 데이터, 로우 데이터, 윈도우 이벤트 등의 다양한 소스로부터, 동시에 데이터를 수집한다.
다양한 플러그인을 상요해 데이터를 집계 및 보관, 서버 데이터 처리, 파이프라인으로 데이터를 수집하여 필터를 통해 변환한다. 마지막으로 ElasticSearch로 전송한다.
#### K (Kibana)
사용자 application으로 ElasticSearch에 저장된 데이터를 시각적으로 탐색하고 실시간으로 분석할 수 있다.
### 1.2. ELK Stack
기존의 ELK 솔루션에서 Beats 추가가 되면서 ELK Stack이라 부른다.
#### Beats
서버에 에이전트로 설치하여 다양한 유형의 데이터를 Elastic Search 또는 Logstash에 전송하는 오픈 소스 데이터 발송자.
## 2. Elastic Search
## 2.1. 개념
### 1. Near Realtime
거의 실시간 검색 플랫폼이라는 특징을 가짐
### 2. Cluster
전체 데이터를 함께 보유하고 모든 노드에서 연합 인덱싱 및 검색 기능을 제공하는 하나 이상의 노드 모음.

Elasticsearch에서 가장 큰 시스템 단위를 의미

기본적으로 'elasticsearch'라는 고유한 이름으로 식별

서로 다른 cluster는 데이터의 접근, 교환을 할 수 없는 독립적인 시스템으로 유지

여러 대의 서버가 하나의 클러스터를 구성할 수 있고, 한 서버에 여러 개의 클러스터가 존재할 수 있다.
### 3. Node
클러스터의 일부이며 데이터를 저장하고 클러스터의 인덱싱 및 검색 기능에 참여하는 단일 서버

노드에 할당되는 임의 UUID인 이름으로 식별

특정 클러스터를 클러스터 이름으로 결합하도록 노드를 구성할 수 있음

역할에 따라 master-eligible, data, ingest, tribe 노드로 구분
#### master-eligible
> 클러스터를 제어하는 마스터로 선택할 수 있는 노드
> * 인덱스 생성, 삭제
> * 클러스터 노드들의 추적, 관리
> * 데이터 입력 시 어느 shard에 할당할 것인지
#### data node
> 데이터와 관련된 CRUD 작업과 관련있는 노드
> 이 노드는 CPU, 메모리 등 자원을 많이 소모하므로 모니터링이 필요하며, master노드와 분리되는 것이 좋다.
#### ingest node
> 데이터를 변환하는 등 사전 처리 파이프라인을 실행하는 역할을 한다.
#### coordination only node
> data node와 master-eligible node의 일을 대신하는 이 노드는 대규모 클러스터에서 큰 이점이 있다.
### 4. Index (관계형 DB의 database)
다소 유사한 특성을 갖는 문서들의 집합

단일 클러스터에서 원하는 만큼의 인덱스를 정의할 수 있음.
### 5. Type (관계형 DB의 Table)
Index 내에서 하나 이상의 Type을 정의할 수 있음.
### 6. Document (관계형 DB의 row)
Index를 생성할 수 있는 기본 정보 단위

JSON으로 표현
### 7. Shards
Index는 잠재적으로 단일 노드의 하드웨어 제한을 초과할 수 있는 많은 양의 데이터를 저장할 수 있다. 하지만 단일 노드의 디스크가 맞지 않거나 단일 노드의 검색 요청만 처리하기에는 너무 느릴 수 있기 때문에 shards를 이용하여 index를 여러 조각으로 나눌 수 있다.

수평적으로 콘텐츠 볼륨을 split/scale 가능하다.

여러 노드에서 잠재적으로 분산을 통해 작업을 분산 및 병렬 처리를 할 수 있으므로 성능/처리량이 향상된다.
### 8. Replication
장애가 발생할 경우 고가용성을 제공. 그렇기 때문에 복제본 shard는 복사된 원본/기본 shard와 동일한 노드에 할당되지 않는다.
## 2.2. 특징
### Scale out
샤드를 통해 규모가 수평적으로 늘어날 수 있다
### 고가용성
Replica를 통해 데이터의 안정성을 보장
### Schema Free
Json문서를 통해 데이터 검색을 수행하므로 스키마 개념이 없다
### Restful
데이터 CURD 작업은 HTTP Restful API를 통해 수행한다.
> SELECT -> GET
> INSERT -> POST
> UPDATE -> PUT
> DELETE -> DELETE
## 2.3. 역색인
Elasticsearch가 빠른 이유는 역색인(inverted index)에 있다.

Elasticsearch는 텍스트를 파싱해서 검색어 사전을 만든 다음, inverted index방식으로 텍스트를 저장한다.
## 3. Logstash
### 1. input : 데이터 수집
입력을 사용하여 Logstash에 데이터를 가져온다.
file, syslog(RFC31654 형식), beats(Filebeat)

    input {
          .
          .
          .
    }

### 2. filter : 데이터 가공
Logstash 파이프 라인의 중간 처리 장치

형식이나 복잡성에 상관없이 설정을 통해 데이터를 동적으로 변환
> 1) grok : 구문 분석 및 임의의 텍스트로 구성
> 2) mutate : 이벤트 필드에서 일반적인 변환을 수행
> 3) drop : 이벤트를 삭제
> 4) clone : 이벤트의 복사본을 만듦.
> 5) geopip : ip 주소의 지리적 위치에 대한 정보를 추가

    filter {
          .
          .
          .
    }

### 3. output : 데이터 출력
Logstash 파이프 라인의 최종 단계

ELasticSearch, Email, ECS, Kafka등 원하는 저장소에 데이터를 전송
> 1) elasticsearch : 이벤트 데이터를 elasticsearch에 전송
> 2) file : 디스크 파일에 기록
> 3) graphite : graphite에 전송
>> graphite : 메트릭을 저장하고 그래프로 작성하는데 사용되는 오픈소스 도구
> 4) statsd : 카운터 및 타이머와 같은 통계를 수신하고 UDP를 통해 전송되며, 하나 이상의 플러그 가능한 백엔드 서비스에 집계를 보내는 서비스

    output {
          .
          .
          .
    }

### 4. codec
입력 또는 출력의 일부로 작동 할 수 있는 스트림 필터
> 1) json : JSON 형식의 데이터를 인코딩하거나 디코딩한다.
> 2) multiline : 자바 예외 및 스택 추적 메시지와 같은 여러 줄 텍스트 이벤트를 단일 이벤트로 병합한다.
## 4. Beats
1. FileBeat
> 서버에서 로그파일을 제공한다.
2. PackerBeat
> 응용 프로그램 서버간에 교환되는 트랜잭션에 대한 정보를 제공하는 네트워크 패킷 분석기
3. MetricBeat
> 운영 체제 및 서비스에서 Metrics를 주기적으로 수집하는 서버 모니터링 에이전트
4. WinlogBeat
> Windows 이벤트 로그 제공
## 5. X-Pack
기존 ELK Stack 에서 x-pack을 이용하여 구축 및 유지를 편리하게 하줄 솔루션.
> 1. Security : 데이터 보호
> 2. Alerting : 데이터 변경 사항에 대한 알림 제공
> 3. Monitoring : ELK Stack 상태를 지속적으로 체크
> 4. Reporting : 주기적으로 보고서를 생성하여 이메일로 전송
> 5. Graph : 데이터에서 의미 있는 관계를 살필 수 있음.
