Advanced Integration Labs with Red Hat Fuse
--
Project to accomplish tasks defined for the lab homework assignment of Advanced Agile Integration course.

Improvements can be done, as referred in lab description, using assync calls from REST service, given results to client before execution of entire routes chain definition. Also, we should have unit tests for every single route definition, in order to achieve better confidence with defined rules under camelContext.xml files.

Requirements
--
- OpenJDK 1.7+
- Fuse 6.2.1 ($fuse_home)
- Maven 3+

We also must follow all initial setup, done with instructions given from:
https://github.com/gpe-mw-training/experienced-integration-labs/tree/master/code/06_homework-assignment


How to build solutions
--
Solution deployments done into Karaf.

First we need to create our queues into Fuse Console (http://127.0.0.1:8181/). Login with admin/admin credentials, click into ActiveMQ tab and create following queues: 

 - q.empi.deim.in
 - q.empi.nextgate.dlq 
 - q.empi.nextgate.out
 - q.empi.transform.dlq

Subprojects
---
Solutions are given under core/ directory of the project

inbound 
---
https://github.com/felipe-duarte/advanced-integration-labs/tree/master/core/inbound

Synchronous call to routes from *direct:integrateRoute*, which exposes JAX-RS Rest service at following URL: *localhost:9098/cxf/demos/match* . Then we marshall ``_marshalToPerson`` with customized dataFormat, and send the contents message to *q.empi.deim.in queue*, defined with InOut pattern, as we must wait response for the entire route path execution.

At the end of execution, we extract resultCode from Nextgate-WS response, and generate ESBResponse to JAX-RS client, based into resultCode.

Route definition located at *resources/META-INF/spring/camelContext.xml*

*How to build and do hot deployment into Karaf:*
  ```
  mvn clean install
  ```
Now we can copy generated bundle jar file from  *target/inbound-1.0-SNAPSHOT.jar*  into  **$fuse_home/deploy**
 
xlate
---
https://github.com/felipe-duarte/advanced-integration-labs/tree/master/core/xlate
  
Consume message from *q.empi.deim.in queue*, unmarshall message, execute transformation (TransformToExecuteMatch), marshall the message and send it to *q.empi.nexgate.out*.

Route definition located at *resources/META-INF/spring/camelContext.xml* 
 
*How to build and do hot deployment into Karaf:*
```
mvn clean install 
```
Now we can copy generated bundle jar file from  *target/xlate-1.0-SNAPSHOT.jar*  into  **$fuse_home/deploy**
    
outbound
---
https://github.com/felipe-duarte/advanced-integration-labs/tree/master/core/outbound

Consume message from *q.empi.nextgate.out* and send it to Nextgate-WS URL.

Route definition located at *resources/META-INF/spring/camelContext.xml*

*How to build and do hot deployment into Karaf:*
 ``` 
 mvn clean install
 ``` 
Now we can copy generated bundle jar file from *target/outbound-1.0-SNAPSHOT.jar*  into  **$fuse_home/deploy**  


At the end of those tasks, we should have the inboundRest, xlatePerson and outboundSOAP routes defined into our contexts, which can be found in Camel tab of Fuse Console.

Testing
--
After projects deployments into Karaf, we can verify routes execution with SOAP-UI, using given project defined in Customer-App-Demo-soapui-project.xml, found under tooling/ folder. 

We may also verify logs and route execution from Camel Contexts, at Camel tab in Fuse Console.

Results of SOAP-UI Requests (MATCH and NO MATCH) , must be like those following samples:

MATCH - Sample Response
---
```
<ESBResponse xmlns="http://www.response.app.customer.com">
   <BusinessKey>92d01013-ce81-4f86-8ea1-fed59b03464f</BusinessKey>
   <Published>true</Published>
   <Comment>MATCH</Comment>
</ESBResponse>
```

NO MATCH - Sample Response
---
```
<ESBResponse xmlns="http://www.response.app.customer.com">
   <BusinessKey>3925a94b-8a06-40e5-a791-672da7e379e6</BusinessKey>
   <Published>true</Published>
   <Comment>NO MATCH</Comment>
</ESBResponse>
```
