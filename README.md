
<img src="img/1.jpg">


* Ingest Node - light version of logstash
* Logstash - processing and parsing

### Elasticsearch

Search and Analytics Engine

* Replicate shards - better search throughput


### Setup

<img src="img/2.jpg">

```
# master node , data node 
sudo su
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
curl -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.0-x86_64.rpm
rpm --install elasticsearch-7.6.0-x86_64.rpm
systemctl enable elasticsearch
```

```
# master node , data node 

vim /etc/elasticsearch/elasticsearch.yml

```

### change heap size

```
vim /etc/elasticsearch/jvm.options

-Xms768m
-Xmx768m
```

### start elasticsaerch

```
systemctl start elasticsearch
systemctl status elasticsearch

less /var/log/elasticsearch/playground.log
```

```
curl localhost:9200
curl localhost:9200/_cat/nodes

```

## Kibana

setup on master node
```
curl -O https://artifacts.elastic.co/downloads/kibana/kibana-7.6.0-x86_64.rpm
rpm --install kibana-7.6.0-x86_64.rpm
```

```
vim /etc/kibana/kibana.yml
```

```
systemctl start kibana
```

```
less /var/log/messages

<master node public ip>:8080
```

```
GET _cat/nodes?v
PUT 
DELETE 
```

### bulk index data 

```
GET _cat/nodes?v
GET _cat/indices?v

PUT bank 
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}

PUT shakespeare
{
  "mappings": {
    "properties": {
      "speaker":{
        "type":"keyword"
      },
      "play_name":{
        "type":"keyword"
      },
      "line_id":{
        "type":"integer"
      },
      "speech_number":{
        "type":"integer"
      }
    }
  },
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}

PUT logs
{
  "mappings": {
    "properties": {
      "geo":{
        "properties": {
          "coordinates":{
            "type":"geo_point"
          }
        }
      }
    }
  },
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}
```

```
cd /etc/elasticsearch
curl -O https://raw.githubusercontent.com/linuxacademy/content-elasticsearch-deep-dive/master/sample_data/accounts.json
curl -O https://raw.githubusercontent.com/linuxacademy/content-elasticsearch-deep-dive/master/sample_data/logs.json
curl -O https://raw.githubusercontent.com/linuxacademy/content-elasticsearch-deep-dive/master/sample_data/shakespeare.json
```

* no account / no cert bulk upload
```
curl -H "Content-Type:application/x-ndjson" -XPOST "localhost:9200/bank/_bulk"  --data-binary "@accounts.json"
curl -H "Content-Type:application/x-ndjson" -XPOST "localhost:9200/shakespeare/_bulk"  --data-binary "@shakespeare.json"
curl -H "Content-Type:application/x-ndjson" -XPOST "localhost:9200/logs/_bulk"  --data-binary "@logs.json"
```

* refresh data 
```
POST /bank/_refresh
POST /shakespeare/_refresh
POST /logs/_refresh
```

* head data
```
```