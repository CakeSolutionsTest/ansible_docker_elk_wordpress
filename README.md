# ansible_docker_elk_wordpress
Dockerized WordPress application


# Introduction

This Playbook describes the design and implementation of the docker compose based application deployment and centralized logging using Elasticsearch, Logstash and Kibana stack.

Inside the EC2 instance, the services are setup in a container based
  system. Here we have mainly five services:
  1. Elasticsearch
  2. Logstash
  3. Kibana
  4. Application - Wordpress
  5. Mysql database

  So we will have five docker container in our setup.

** Communication network ports
   |---------------+----------------------------------------------------------------------------|
   | *Service      | Listens on*                                                                |
   |---------------+----------------------------------------------------------------------------|
   | wordpress     | 0.0.0.0:8081->80/tcp                                                       |
   |---------------+----------------------------------------------------------------------------|
   | mysqldb       | 3306/tcp                                                                   |
   |---------------+----------------------------------------------------------------------------|
   | kibana        | 0.0.0.0:5601->5601/tcp                                                     |
   |---------------+----------------------------------------------------------------------------|
   | logstash      | 0.0.0.0:5000->5000/tcp, 0.0.0.0:25826->25826/tcp, 0.0.0.0:12201->12201/udp |
   |---------------+----------------------------------------------------------------------------|
   | elasticsearch | 0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp                             |
   |---------------+----------------------------------------------------------------------------|
   | Host          | 0.0.0.0:22                                                                 |
   |---------------+----------------------------------------------------------------------------|


** Provisioning EC2
   AWS instance is launched using Ansible playbook. Ansible
   authenticates to aws using user access and secret key. 

 Run playbook
      #+BEGIN_EXAMPLE
      ansible-playbook -i hosts provision.yml -vvvv
      #+END_EXAMPLE


** Configuring EC2
   EC2 instance is configured via Ansible playbook. It installs the
   docker-engine, docker-compose and other required packages.

   Run playbook to configure the ec2 container and setup docker.
   #+BEGIN_EXAMPLE
   ansible-playbook configure-ec2.yml -i hosts --private-key  <path-to-keypair>
   #+END_EXAMPLE

** Create containers
*** ELK Stack
    ELK stack is configured via Ansible playbook. Which internally
    uses docker-compose file to setup elk stack. Customized images are
    build from the official docker images. Then services inside the
    container are started.
    

    To do the setup run ansible playbook as follows:
    #+BEGIN_EXAMPLE
    ansible-playbook -i ../hosts deploy-elk.yml --private-key <keypair>
    #+END_EXAMPLE

*** Application
    Application is configured via ansible playbook. Which internally
    uses docker-compose file. It setups the wordress application in
    one container and database service inside another container.

    Run the ansible playbook as follows:
    #+BEGIN_EXAMPLE
    ansible-playbook -i ../hosts deploy-app.yml --private-key <keypair>
    #+END_EXAMPLE

* Install Wordpress
  After the setup we can go to url: [[http://<vm-ip>:8081]] to install
  the wordpress.