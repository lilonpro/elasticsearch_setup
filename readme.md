# Elasticsearch and Kibana setup
## Elasticsearch setup
### elasticsearch.yml
#### single node config
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
#### Settting up cluster
4. discovery.seed_hosts: masterIp0:port, masterIp1:port
    - This is this list of all master (eligable) node, for the sake of simplicity lets put ip and port on in here
    - For our **LOVELY Sanjay**: Replace following list with the server host name list in elasticsearch.yml
    - 192.168.1.29:9300, 192.168.1.2:9300
5. cluster.initial_master_nodes: md0,md1
    - The node names of master (eligable) nodes (read more if you prefer)
6. network.publish_host: 192.168.1.29
    - Publish address, if this is not set it will take one of network.host which it thinks the best. So we need to set it if we have multiple network.host binding
    - For our **LOVELY Sanjay**: Replace this with each server host name/ip, it should be in the discovery.seed_hosts
    