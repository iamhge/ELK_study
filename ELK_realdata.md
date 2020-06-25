# ELK 활용 - 실데이터 분석 실습
본 실습은 inflearn의 허민석님의 강의와 github ch06의 자료를 토대로 진행하며 작성하였음.  
## 1. 인구분석

    $ <elasticsearch 패키지 위치>/bin/elasticsearch -d
    $ sudo systemctl start elasticsearch.service
    $ sudo systemctl start kibana.service
    $ sudo <logstash 패키지 위치>/bin/logstash -f ~/<logstash.conf 파일 위치>/logstash.conf

* ES, kibana를 먼저 실행시킨다.
* logstash를 실행시킨다.  

![ELK_logstashconf](./elk_image/ELK_logstashconf.png)  
<logstash.conf>  

![Kibana_starting](./elk_image/Kibana_starting.png)  
<Kibana 시작 화면>  

![ELK_kibaba_1](./elk_image/ELK_kibana_1.png)  
<Kibana에서 population index create>  

![ELK_kibaba_2](./elk_image/ELK_kibana_2.png)  
<next step 후 time stamp 설정>  

![ELK_kibaba_3](./elk_image/ELK_kibana_3.png)  
<population 확인>  

![ELK_kibaba_4](./elk_image/ELK_kibana_4.png)  
<discover 확인>  

![ELK_kibaba_5](./elk_image/ELK_kibana_5.png)  
<toggle을 사용하여 원하는 정보만 pick>  

![ELK_population_dash](./elk_image/ELK_population_dash.png)  
<population dash board>

## 2. 주식 분석

1. 검색창에 yahoo stock을 검색한다.  
2. yahoo finance에 들어가 주식 분석을 원하는 기업명을 서치한다. ex) NFLX, FB, ...
3. time period를 설정하고 apply 후 download 받는다. 


    $ sudo <logstash 패키지 위치>/bin/logstash -f ~/<logstash.conf 파일 위치>/logstash_stock.conf

![ELK_logstashstockconf](./elk_image/ELK_logstashstockconf.png)  
<logstash_stock.conf>  

![ELK_stock_log](./elk_image/ELK_stock_log.png)  
<위의 logstash 실행 후 로그 출력 정보>    

![ELK_stock_1](./elk_image/ELK_stock_1.png)  
<time stamp 설정>  

![ELK_stock_2](./elk_image/ELK_stock_2.png)    
<stock 확인>  

![ELK_stock_3](./elk_image/ELK_stock_3.png)  
<discover 확인>  

![ELK_stock_dash](./elk_image/ELK_stock_dash.png)  
<stock dash board>  
