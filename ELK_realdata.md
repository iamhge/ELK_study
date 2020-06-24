# ELK 활용 - 실데이터 분석 실습
본 실습은 inflearn의 허민석님의 강의와 github ch06의 자료를 토대로 진행하며 작성하였음.  
## 1. 인구분석

    $ <elasticsearch 패키지 파일>/bin/elasticsearch -d
    $ sudo systemctl start elasticsearch.service
    $ sudo systemctl start kibana.service
    $ sudo <logstash 파일 위치>/bin/logstash -f ~/<logstash.conf 파일 위치>/logstash.conf

* ES, kibana를 먼저 실행시킨다.
* logstash를 실행시킨다.  

![ELK_logstashconf](./elk_image/ELK_logstashconf.PNG)  
<logstash.conf 내용>  


![ELK_kibaba_1](./elk_image/ELK_kibaba_1.PNG)  
![ELK_kibaba_2](./elk_image/ELK_kibaba_2.PNG)  
![ELK_kibaba_3](./elk_image/ELK_kibaba_3.PNG)  
![ELK_kibaba_4](./elk_image/ELK_kibaba_4.PNG)  
![ELK_kibaba_5](./elk_image/ELK_kibaba_5.PNG)  
<>