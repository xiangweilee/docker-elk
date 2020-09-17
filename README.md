# Docker ELK

ELK stack in Docker container

Filebeat + Logstash + Elasticsearch + Kibana in one `docker-compose.yml` file

> Here we use apache.log file as an example

## Start

```shell
docker-compose up -d
```

| <!-- -->      | URL                    |
| ------------- | ---------------------- |
| Elasticsearch | http://localhost:9200/ |
| Kibana        | http://localhost:5601/ |

## Stop

```shell
docker-compose down
```
