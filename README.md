# advanced-integration-labs
Integration Labs - Red Hat Fuse

Red Hat Fuse Integration Labs

Required tools

OpenJDK 1.8+
Maven 3+
Red Hat Fuse 6.2+
SOAP UI or curl 

Setup done as described into: https://github.com/gpe-mw-training/experienced-integration-labs/tree/master/code/06_homework-assignment

Routes are describe with Camel Context (resources/META-INF/spring/camelcontext.xml) 

How to run routes

Sub-project inbound ()
 mvn clean install

Sub-project xlate ()
 mvn clean install

Sub-project outbound ()
 mvn clean install
