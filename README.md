# springBootTests
Provides a quick-start example of using springBoot. 

## Overview
In this tutorial, a java spring boot application is deployed to a tomcat server to support typical API calls.  A tomcat docker configuration is included.

## Requirements
* Docker installed on your local system, see [Docker Installation Instructions](https://docs.docker.com/engine/installation/).

* When using Docker for Mac or Docker for Windows, the default resources allocated to the linux VM running docker are 2GB RAM and 2 CPU's. Make sure to adjust these resources to meet the resource requirements for the containers you will be running. More information can be found here on adjusting the resources allocated to docker.

[Docker for mac](https://docs.docker.com/docker-for-mac/#advanced)

[Docker for windows](https://docs.docker.com/docker-for-windows/#advanced)

## Getting Started
1. Prepare Docker environment-see the Prerequisites section above...
2. Pull this github into a directory
```bash
git clone https://github.com/jphaugla/springBootTests.git
```
3. Refer to the notes each of Docker images used (tomcat) but don't get too bogged down as docker compose handles everything except for a few admin steps on tomcat.
 * [https://hub.docker.com/r/bitnami/tomcat/](https://hub.docker.com/r/bitnami/tomcat/)
4. Open terminal and change to the github home where you will see the docker-compose.yml file, then: 
```bash
docker-compose up -d
```

## Gain access to tomcat administration

By default, Tomcat only allows management access using a browser from the same machine as the browser is running on.  Since this is a docker instance, that must be disabled.  To disable this restriction, two identical files must be edited.  These files can both be found in the docker volume so they can be edited on the docker host.  These are the two files:
```bash
   ./springBootTests/tomcat_data/tomcat/data/host-manager/META-INF/context.xml
   ./springBootTests/tomcat_data/tomcat/data/manager/META-INF/context.xml
```

The same line must be commented out on each file 

```bash
<Context antiResourceLocking="false" privileged="true" >
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
</Context>
```
then, restart the docker instances
```bash
docker-compose restart
```

## The spring boot java code

The java code demonstrates common API actions with the data persisted in REDIS.  The java spring Boot framework mminimizes the amount of code to build and maintain this solution.  Maven is used to build the java code and the code is deployed to the tomcat server.

## To execute the code

1. Complile the code
```bash
mvn package
```
2. Open the tomcat web application manager page.  NOTE:  this must be http and not https.  Some browsers (safari) stubbornly substitute http with https.
```bash
http:/localhost:8080/manager/html
```
3.  Deploy the war file.   in the web application manager page in the "WAR file to deploy section", click on "Choose File".  Find the war file in the target directory.  The path should resemble:  "/Users/somemacuser/gits/springBootTests/target/demo-0.0.1-SNAPSHOT.war".  
Deploy the application.
4.  Test the application
  * retrieve values in store
```bash
curl localhost:8080/demo-0.0.1-SNAPSHOT/products/
```
  * POST values in store
```bash
curl -d '{"id":3, "name":"Milk"}' -H "Content-Type: application/json" -X POST http://localhost:8080/demo-0.0.1-SNAPSHOT/products/
```
  * PUT  value in store
```bash
curl -d '{"name":"bread"}' -X PUT -H "Content-Type: application/json" localhost:8080/demo-0.0.1-SNAPSHOT/products/5/
```




