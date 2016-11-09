docker-compose for Twitter + Kafka 0.8.2.1  + Logstash 2.3 + ElasticSearch 2.3
=============================

### Update configuration of twitter-2-kafka.config 
1. To update the key , secret , token by your Twitter 

		input {
			twitter {
				# update data
				consumer_key => "YOUR_CONSUMER_KEY"
				consumer_secret => "YOUR_CONSUMER_SECTET"
				oauth_token => "YOUR_OAUTH_TOKEN"
				oauth_token_secret => "YOUR_OAUTH_TOKEN_SECRET"

				keywords => ["NBA"]
				full_tweet => true
			}
		}		





2. To update the "keywords"


### Update configuration of docker-compose.yml 
1. Update field of 'ADVERTISED_HOST' to your host ip address. 
   (localhost is not working)

### To run the container
	docker-compose up


### To connect the head plugin of elasticsearch
	http://localhost:29200/_plugin/head/


### The detail of docker-compose.yml
```YML
kafka1:
  image: spotify/kafka
  ports:
    - "2181:2181" 
    - "9092:9092"
  environment:
    ADVERTISED_HOST: 192.168.30.191
    ADVERTISED_PORT: 9092

esearch:
  image: lucasko/elasticsearch
  user: elsearch
  ports:
    - "29200:9200" 
    - "29300:9300" 
  restart: always

logstash1:
  image: logstash
  links:
    - kafka1
  volumes:
    - $PWD/logstash-config:/config-dir
  command: -f "/config-dir/twitter-2-kafka.config"
  restart: always

logstash2:
  image: logstash
  links:
    - kafka1
    - esearch
  volumes:
    - $PWD/logstash-config:/config-dir
  command: -f "/config-dir/kafka-2-elasticsearch.config"
  restart: always

```


