# Elasticsearch and Kibana setup
Note: Any where you see **LOVELY Snjy&Pysh** mean that's the config you need to change on your own server.
## Elasticsearch setup
### elasticsearch.yml
1. cluster.name: es-dce-scc
2. node.name:
    - For our **LOVELY Snjy&Pysh**: set value for each server with md0, md1, md2. You will need to have at least three master eligable node for each cluster. And if you have multiple data center you might **HAVE** to replicate clusters instead of have nodes of same cluster across data centers (?)
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
    - For our **LOVELY Snjy&Pysh**: Replace following list with the server host name list in elasticsearch.yml
    - 192.168.1.29:9300, 192.168.1.2:9300
5. cluster.initial_master_nodes: md0,md1
    - The node names of master (eligable) nodes (read more if you prefer)
6. network.publish_host: 192.168.1.29
    - Publish address, if this is not set it will take one of network.host which it thinks the best. So we need to set it if we have multiple network.host binding
    - For our **LOVELY Snjy&Pysh**: Replace this with each server host name/ip, it should be in the discovery.seed_hosts
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
10. Add cert passphrase to keystore by running following command. The key store will be default to config/elasticsearch.keystore
````
bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
````
11. Setup inital password for reserved users. After step 10 you will not able to view any index until you do this step. The password use for this sample is dce2020, this password will be saved in the internal build in security data which is not included here.
````
.\elasticsearch-7.4.2\bin\elasticsearch-setup-passwords interactive
Initiating the setup of passwords for reserved users elastic,apm_system,kibana,logstash_system,beats_system,remote_monitoring_user.
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y


Enter password for [elastic]: 
Reenter password for [elastic]: 
Enter password for [apm_system]: 
Reenter password for [apm_system]: 
Enter password for [kibana]: 
Reenter password for [kibana]: 
Enter password for [logstash_system]: 
Reenter password for [logstash_system]:
Enter password for [beats_system]:
Reenter password for [beats_system]:
Enter password for [remote_monitoring_user]:
Reenter password for [remote_monitoring_user]:
Changed password for user [apm_system]
Changed password for user [kibana]
Changed password for user [logstash_system]
Changed password for user [beats_system]
Changed password for user [remote_monitoring_user]
Changed password for user [elastic]
````
12. Verify you can access localhost:9200 with user elastic and the password you just set.
## Kibana setup
### config/kibana.yml
13. server.host: Kibana running server host names
    - - For our **LOVELY Snjy&Pysh**: Replace this with the host name of where kibana is running
14. elasticsearch.hosts: urls of all elastic search nodes (See elastic config for host and port)
    - - For our **LOVELY Snjy&Pysh**: Replace this with the server urls
15. Elastic user name and passwrod for kibana. Password was set in step 11.
    - elasticsearch.username: "kibana"
    - elasticsearch.password: "dce2020": 
16. Kiabana will be up and running on port 5601. **NOTE" you will only be able to access Kibana with whatever you set in server.host
## HTTPS setup for Elastic and Kibana (TO BE CONTINUED)
    - Ref.: https://www.elastic.co/guide/en/elasticsearch/reference/7.4/configuring-tls.html#tls-http
## Critical
    - Setup JVM HEAP size properly base on documentation
    - Try to idenfity any other optimization you have in your older version of Elastic cluster if you have one.