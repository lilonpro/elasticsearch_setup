# Elasticsearch and Kibana setup
## Elasticsearch setup
### elasticsearch.yml
#### single node config
1. cluster.name: es-dce-scc
2. node.name:
    - For our **LOVELY Sanjay**: set value for each server with md0, md1, md2. We will have to do 3 scc and 3 gcc as single master and data node in gcc will not work (?)
    - More about node types: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html
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
7. **NO LONGER NEEDED** discovery.zen.minimum_master_nodes
    - In version 7 elastic auto manages the size of the voting set of master nodes itself. So even if you set it, it will gets ignored.
    - Read more: https://discuss.elastic.co/t/avoid-split-brain-with-new-elasticsearch-7-0-after-discovery-zen-minimum-master-nodes-removal/176877
#### Setting up TLS
Ref.: https://www.elastic.co/guide/en/elasticsearch/reference/7.4/configuring-tls.html
8. Run command to generated the certificate into config folder. Keep a note for the keyphase. The keyphrase for the sample is dce2020
````
.\elasticsearch-7.4.2\bin\elasticsearch-certutil cert
````
9. Enable security and set cert path
````
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate 
xpack.security.transport.ssl.keystore.path: certs/elastic-certificates.p12 
xpack.security.transport.ssl.truststore.path: certs/elastic-certificates.p12 
````