# Exporting cAdvisor Stats to ElasticSearch

cAdvisor supports exporting stats to [ElasticSearch](https://www.elastic.co/). To use ES, you need to provide the additional flags to cAdvisor:

Set the storage driver as ES:

```
 -storage_driver=elasticsearch
```

Specify ES host address:

```
 -storage_driver_es_host="http://elasticsearch:9200"
```

There are also optional flags:

```
 # ElasticSearch type name. By default it's "stats".
 -storage_driver_es_type="stats"
 # ElasticSearch can use a sniffing process to find all nodes of your cluster automatically. False by default.
 -storage_driver_es_enable_sniffer=false
```

## ElasticSearch 5.x support

### Using docker:
Using docker run:

```
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8888:8080 \
  --detach=true \
  --name=cadvisor \
  asedmammad/cadvisor-es5:latest \
  -storage_driver="elasticsearch.v5" --housekeeping_interval=3s -storage_driver_es_host="http://localhost:9200" -storage_driver_es_basic_auth "user:password" -storage_driver_es_index="cadvisor-{2006.01.02}"
```

Using docker swarm services:

```
docker service create --name=cadvisor --mode global --mount type=bind,src=/,dst=/rootfs,ro=1 --mount type=bind,src=/var/run,dst=/var/run,ro=0 --mount type=bind,src=/sys,dst=/sys,ro=1 --mount type=bind,src=/var/lib/docker/,dst=/var/lib/docker,ro=1 --publish mode=host,published=8888,target=8080,protocol=tcp --detach=true asedmammad/cadvisor-es5:latest --housekeeping_interval=3s -storage_driver="elasticsearch.v5" -storage_driver_es_host="http://localhost:9200" -storage_driver_es_basic_auth="user:password" -storage_driver_es_index="cadvisor-{2006.01.02}"
```

Options:

Set the storage driver as ES:

```
 -storage_driver=elasticsearch.v5
```
There are also optional flags:

* -storage_driver_es_basic_auth (ElasticSearch basic auth: user:password)
* -storage_driver_es_sniffer_timeout (The time before Elastic times out on sniffing nodes in seconds)
* -storage_driver_es_sniffer_timeout_startup (The sniffing timeout used while creating a new client)
* -storage_driver_es_sniffer_interval (The interval between two sniffer processes)
* -storage_driver_es_enable_health_check (Enable health check)
* -storage_driver_es_health_check_timeout (The timeout for health checks)
* -storage_driver_es_health_check_timeout_startup (The health check timeout used while creating a new client)
* -storage_driver_es_health_check_interval (The interval between two health checks)

all options explaination can be found at  project [asedmammad/elastic](https://github.com/asedmammad/elastic/wiki).

### index name time support
Enable golang time format support for option **storage_driver_es_index**.

#### Example
```
  -storage_driver_es_index="cadvisor-{2006.01.02}"
```
Data will be exported to index cadvisor-2017.01.01 on day 2017/01/01, index cadvisor-2017.02.12 on day 2017.02.12.


# Examples

For a detailed tutorial, see [docker-elk-cadvisor-dashboards](https://github.com/gregbkr/docker-elk-cadvisor-dashboards)
