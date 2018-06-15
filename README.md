# elk + beat
elasticsearch + logstash + kibana + filebeat

## filebeat-6.2.3

경량 로그 수집기 filebeat을 설치해보자!!


* rpm 설치 :
```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.2.3-x86_64.rpm
sudo rpm -vi filebeat-6.2.3-x86_64.rpm
```

* log 위치 : 
```bash
cd /var/log/filebeat/filebeat
```

* config : /etc/filebeat/filebeat.yml 
```bash
filebeat.prospectors:
- type: log
  enabled: true
  paths:
    - /var/log/*.log #전송할 로그 위치
#------- Logstash output -------
output.logstash:
  # The Logstash hosts
  hosts: ["127.0.0.1:5044"]
```

* run filebeat : 
```bash
service filebeat start
```

## logstash-6.2.3

데이터 처리 파이프라인으로 동시에 다양한 데이터를 수집하자!! (java 확인)

* Download and install the public signing key:
```bash
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

* /etc/yum.repos.d/logstash.repo 생성 
```bash
[logstash-6.x]
name=Elastic repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

* yum 설치 : 
```bash
sudo yum install logstash
```bash

* plugin update : logstash-input-beats 
```bash
/usr/share/logstash/bin/logstash-plugin update logstash-input-beats
```

* /etc/logstash/conf.d/logstash-simple.conf
```bash
input {
    beats {
        port => 5044
    }
}
filter {
}
output {
    elasticsearch {
        hosts => "localhost:9200"
        manage_template => false
        index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
        document_type => "%{[@metadata][type]}"
    }
}
```

* /etc/logstash/logstash.yml
```bash

```

* run logstash : 
```bash
/usr/share/logstash/bin/logstash --path.settings /etc/logstash/ -f /etc/logstash/conf.d/logstash-simple.conf
```

## elasticsearch-6.2.3

쉽게 검색엔진을 경험하자!!

* Download 
```bash
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.tar.gz
tar -xvf elasticsearch-6.2.3.tar.gz
```

* run elasticsearch
```bash
cd elasticsearch-6.2.3/bin
./elasticsearch
```

## kibana-6.2.3

kibana와 데이터 시각화하기

* kibana 설치하기
```bash
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.3-linux-x86_64.tar.gz
shasum -a 512 kibana-6.2.3-linux-x86_64.tar.gz 
tar -xzf kibana-6.2.3-linux-x86_64.tar.gz
cd kibana-6.2.3-linux-x86_64/ 
```

* run kibana
```bash
./bin/kibana
```
