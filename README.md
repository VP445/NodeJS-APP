# NodeJS-APP

## Pre-Requisites

You shoudl have the following packages installed on the Linux System.

* docker
* docker-compose

## Changes Done 

* Since this is a docker-based deployment, and the docker-container's communicate to each other with either the docker-IP of the container or, using the name given to the docker-container.
* The demerit of using the dockerIP fr communication is, that the IP changes if the docker container is stopped/restared. Therefor the better option to use for communicating with the docker container's is using the name of the docker containers.
* First Change : In the file : config/default.json, we have changed the host of both the mongo and mysql specifications, so that when we deploy the node application it should know which host to connect for mongo and sql since the name of the containers will be same

  ![Alt text](./images/config_changes.JPG?raw=true "Title")

* Created a  Dockerfile for dockerizing the provided node application.
* Command to build the Dockerfile manually.
```
   docker build -t node_docker:0.1 .
```
* Created a docker-compose.yml file which will be used for deploying the docker-containers of the node application, mongo and mysql.
  ![Alt text](./images/docker-compose.JPG?raw=true "Title")
  
* IMP : In order to create a connection from one docker-container to another, the easiest way is to keep all the docker-container's within the same docker-network. Therefor in the docker-compose files we are implicitely creating a docker network with the name "gateway". Refer to the image below.
  ![Alt text](./images/docker-network.JPG?raw=true "Title")
  
* Definition of Services in docker-compose file : 
  * There was a requirement of 3 services in the following docker deployment, thus we have defined node, mongo and mysqldb as 3 services in the docker-compose files which will be deployed as 3 docker containers, when we do a docker-compose up.
  * There is a dependency of the node and the mongo docker containers on the mysql container, so we have defined the "depends_on" field in the compose file in the configurations for the node and mongo , as shows below .
  
  ![Alt text](./images/dependency.JPG?raw=true "Title")

  * We have also exposed all the default ports of the container on which the services are provided for the application in the docker-compose definiton.
  
* Nginx Configuration : 
  * Since the deployment had to be driven using nginx, there for we are installing the nginx package on the node docker container using the following command writen in the entrypoint.sh file :
    
  ```
  apt update
  apt install nginx -y
  ```
  * We are also putting our own customized nginx.conf file which as configurations to redirect http to https, and also to deploy the container with locally generated certificates.
  * Certificate generation is being done during the process when the node container is being brough up. Following command is used for the local certificate generation, and is mentioned in the entrypoint.sh file. 
  ```
  openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout certificates/localhost.key -out certificates/localhost.crt -subj "/C=GB/ST=London/L=London/O=Global Security/OU=IT Department/CN=example.com"
  ```
  * The nginx.conf file has reference to thee locally generated certificates while getting served over nginx.
  * NOTE: These are locally generated, therefore you might see not trusted message while trying to access it over the browser.
  
 
## Steps to deploy

* Change the server_name to the public_IP of your server in the nginx.conf file.(highlighted)
  ![Alt text](./images/server_name_change.JPG?raw=true "Title")
* docker-compose up -d
  