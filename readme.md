# Elasticsearch and Kibana setup
## Elasticsearch setup
### elasticsearch.yml
1. cluster.name: es-dce-scc
2. node.name:
    - md-node: master and data node
    ````
    node.master: true
    node.data: true
    ````
    - m-node: master only node
    ````
    node.master: true
    node.data: false
    ````
    - d-node: data only node
    ````
    node.master: false
    node.data: true
    ````
3. network.host:_local_,_site_
    - Note: without discovery settings, single node can only start when network.host is set to loopback address. 
    otherwise node will fail bootrap check and faile to start.
    - https://www.elastic.co/guide/en/elasticsearch/reference/7.4/discovery-settings.html
    - _local_: localhost
    - _site_: server ip address