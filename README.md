<img src="https://th.bing.com/th/id/OIP.CwPiU5tKuxQpL4ZMRSoVIQAAAA?pid=ImgDet&rs=1" alt="AMD Pensando" width="350"/>


ELK based analytics for Pensado Systems

This repository is the starting point for building and utlizing the Elasticstack for monitoring and analyzing
data, both about and traversing, the Pensando DSS(es) within your environment.  The purpose is to consolidate the
applications and tools used for said monitoring and analysis and deploy them in an easy fashion.

Instantiation can be done on any system with docker and docker-compose installed.

## Running ELK-Pensando


:warning: <span style="color:yellow">**WARNING**</span> :warning:

<mark>DO NOT RUN THE INSTALL OR CONFIGURATION AS ROOT!!!  IT WILL NOT WORK. </mark>


<br/>

---
**NOTE**

This branch works with the following software. <br/>

CXOS: 10.13.x <br/>
PSM:  1.59.0-50 or later

If these do not match your current install, [check one of the other branches](https://github.com/amd/pensando-elk/branches)

---
  ### Please fully read the Support Policy below if you are having problems installing or configuring this

  #### Installation and running

  1. Verify you are on the correct branch before starting
        ```
        git branch
        ```

  2. If you are not, use the following command to switch to the correct branch:
        ```
        git checkout aoscx_10.13
        ```

  3. run the following command (change 8.6.2 if the version of ELK you want is different):
      ```
      echo "TAG=8.11.0" >.env
      ```

  4. Create the following directories and give them full write permissions (777 works)
      ```
      ./data/es_backups
      ./data/pensando_es
      ./data/elastiflow
      chmod -R 777 ./data
      ```

  5. Ensure that you update ```vm.max_map_count``` on your system so that elasticsearch can store it's inidices correctly
      ```
      sudo sysctl -w vm.max_map_count=262144
      echo vm.max_map_count=262144 | sudo tee -a /etc/sysctl.conf
      ```

  6. If you are going to collect IPFix packets, update the following lines in the docker-compose.yml file with your information:


        Change false to true
        ``` bash
            EF_OUTPUT_ELASTICSEARCH_ENABLE: 'true'
        ```

        Change the "CHANGEME" in this line to the IP address of your system.  Do not use localhost or the loopback, it will not work
        ``` bash
            EF_OUTPUT_ELASTICSEARCH_ADDRESSES: 'CHANGEME:9200'
        ```

  7. Using PSM, point your DSS firewall syslog (RFC5424) at the IP of your ELK cluster, UDP port 5514  (this number can be changed in the logstash/dss_syslog.conf file in the input section at the top)*

  8. If collecting IPFix, use PSM point your DSS IPFix flows (flow export policy) at the IP of your ELK cluster, UDP port 9995  (this port number can be changed in the docker-compose file using the EF_FLOW_SERVER_UDP_PORT parameter)*

  10. Run

     If using docker-compose v1 (standalone)

     `docker-compose -d up`

     Or if using docker-compose v2 (docker plug-in)

     `docker compose up --detach`

  11. From the install directory, load the elasticsearch schema (mappings) for the Pensando DSS Firewall index-pattern using the following cli:

     `curl -XPUT -H'Content-Type: application/json' 'http://localhost:9200/_index_template/pensando-fwlog?pretty' -d @./elasticsearch/pensando_fwlog_mapping.json`

  12. Give it about 5 minutes to start up and point your browser to the ip of your ELK cluster, port 5601

  13. In Kibana, import ```./kibana/pensando-dss-elk.ndjson``` into your saved objects

  14. If collecting IPFix, in Kibana import ```./kibana/kibana-7.17.x-flow-codex.ndjson``` into your saved objects

  15. Use basic docker commands, like ```docker ps``` and ```docker logs <container name>``` to view status of how the containers are doing -

*NOTE: It could take about 5 mins for visualizations to become populated in both the DSS and IPFix dashboards.

## Support Policy
The code and templates in the repo are released under an as-is, best effort, support policy. These scripts should be seen as community supported and AMD Pensando will contribute our expertise as and when possible. The absolute best (and quickest) way to get help/support is to [file an issue](https://github.com/amd/cx10000-elastic/issues).  Any other attempts at contact will probably be lost in the ether and you will rarely, if ever, hear back.
