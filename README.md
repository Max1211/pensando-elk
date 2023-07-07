<img src="https://www.amd.com/system/files/styles/992px/private/2022-05/1423875-pensando-logo-white-1260x709_0.png?itok=D5gjoCQP" alt="AMD Pensando" width="350"/>


ELK based analytics for Pensado Systems

This repository is the starting point for building and utlizing the Elasticstack for monitoring and analyzing
data, both about and traversing, the Pensando DSS(es) - DSC(s) coming soon - within your environment.  The purpose is to consolidate the
applications and tools used for said monitoring and analysis and deploy them in an automated fashion.

Instantiation can be done on any system with docker and docker-compose installed.

## Running ELK-Pensando


:warning: <span style="color:yellow">**WARNING**</span> :warning:

<mark>DO NOT RUN DOCKER AS ROOT!!!  IT WILL NOT WORK. </mark>


<br/>

---
**NOTE**

This branch works with the following software.

CXOS: 10.12.x <br/>
PSM:  1.59.0-50 or later

If these do not match your current install, [check one of the other branches](https://gitlab.com/pensando/tbd/siem/elastic/elk-pensando/-/branches)

---

  #### Documented version
  To get up and running with Pensando's ELK implementation, but need a bit more guidance than a basic readme, [click here](https://pensando.gitlab.io/tbd/siem/elastic/elk-pensando/index.html)

  #### Quick Start
  NOTE: Use this if you are running on a *nix system, don't need docker(-compose) help and understand basic ELK admin
  1. Clone this repository

  2. Change into the directory where it is stored

  3. run the following command (change 8.6.2 if the version of ELK you want is different):
      ```
      echo "TAG=8.6.2" >.env
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
            EF_OUTPUT_ELASTICSEARCH_ENABLE: 'false'
        ```
            
        Change the "CHANGEME" in this line to the IP address of your system.  Do not use localhost or the loopback, it will not work
        ``` bash
            EF_OUTPUT_ELASTICSEARCH_ADDRESSES: 'CHANGEME:9200'
        ```

  7. Using PSM, point your DSS firewall syslog (RFC5424) at the IP of your ELK cluster, UDP port 5514  (this number can be changed in the logstash/ taormina.conf file in the input section at the top)*

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

  14. In Kibana, import ```./kibana/kibana-7.17.x-flow-codex.ndjson``` into your saved objects

  15. Use basic docker commands, like ```docker ps``` and ```docker logs <container name>``` to view status of how the containers are doing -

*NOTE: It could take about 5 mins for visualizations to become populated in both the DSS and IPFix dashboards.

## Support
If you need help or have questions, you can [email us](mailto:contact-project+pensando-tbd-elastic-pensando-elk-25427733-issue-@incoming.gitlab.com) and we will get back to you as soon as we can

## Support Policy
The code and templates in the repo are released under an as-is, best effort, support policy. These scripts should be seen as community supported and Pensando will contribute our expertise as and when possible. We do not provide technical support or help in using or troubleshooting the components of the project through our normal support options. Unless explicitly tagged, all projects or work posted in our GitLab repository (at https://gitlab.com/Pensando).
