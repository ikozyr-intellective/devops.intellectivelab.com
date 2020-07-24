# Installing UIE  

## Contents 

[Install Enterprise Index](./installing-uie.html#install-enterprise-index)  
- [Install Solr](./installing-uie.html#install-solr)  
- [Install Lucene](./installing-uie.html#install-lucene) 

[Install Discovery Services](./installing-uie.html#install-discovery-services)  
- [Setup UIE Crawler](./installing-uie.html#setup-uie-crawler)  
- [Install UIE Crawlers](./installing-uie.html#install-uie-crawlers) 
	- [FileNet CE 5.2 crawler](./installing-uie.html#filenet-ce-5.2-crawler) 
	- [CMIS Alfresco crawler](./installing-uie.html#cmis-alfresco-crawler) 
	- [Box crawler](./installing-uie.html#box-crawler) 
	- [CMOD crawler](./installing-uie.html#cmod-crawler) 
	- [CM8 crawler](./installing-uie.html#cm8-crawler) 
	
- [Update UIE Crawlers](./installing-uie.html#update-uie-crawlers) 

[Install Search Services](./installing-uie.html#install-search-services)  
- [Install UIE Searcher on IBM WebSphere](./installing-uie.html#install-uie-searcher-on-ibm-websphere)  
- [Install UIE Searcher on IBM WebSphere Liberty](./installing-uie.html#install-uie-searcher-on-ibm-websphere-liberty)
- [Install UIE Searcher on WebLogic](./installing-uie.html#install-uie-searcher-on-weblogic) 
- [Install UIE Searcher on Wildfly JBoss](./installing-uie.html#install-uie-searcher-on-wildfly-jboss)  

## Install Enterprise Index  

[Solr](https://lucene.apache.org/solr/guide/) is the fast open source search platform built on Apache Lucene™ that provides scalable indexing and search, as well as faceting, hit highlighting and advanced analysis/tokenization capabilities. Solr and Lucene are managed by the [Apache Software Foundation](http://www.apache.org/) 

It can use to implement full-text search, faceted search, and real-time indexing within Unity application. It is required for Unity features such as Faceted Search and Case Management tabs made available in Unity 7.


### Install Solr 

Each Solr installation zip file contains a complete Solr platform package. To install it, simply extract the files from the zip. 

- Create a folder on the installation server \<solr_install\>: 

	![uie-folder](.\images\image11.png) 
	
- Extract all files from the downloaded zip (e.g. solr-8.5.1.zip) to the \<solr_install\> folder: 

	![uie-folder](.\images\image12.png) 
	
- Set `solr.jetty.request.header.size` setting to 65536 from `<solr_install>/server/etc/jetty.xml`: 

	![uie-folder](.\images\image13.png) 
	
	![uie-folder](.\images\image14.png) 
	
- Open Command Prompt and start Solr server using command `solr start`:

	![uie-folder](.\images\image15.png) 	

- Create a new Solr Core using command `solr create -c SODemo`: 

	![uie-folder](.\images\image16.png) 
	
- Verify that SODemo folder is created under \<solr_install\>: 

	![uie-folder](.\images\image17.png) 
	
- Extract `managed-schema` file from the UIE package and copy over to the SODemo core created: 

	![uie-folder](.\images\image18.png) 
	
	![uie-folder](.\images\image19.png) 
	
- Stop the Solr server using `Ctrl+C` on the Command Prompt
- Start the Solr server again using the command `solr start` 
- Verify the Solr server has started successfully by going to Solr Admin using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 

	![uie-folder](.\images\image20.png) 
	
- Verify that the SODemo core is visible in the Solr admin console: 

	![uie-folder](.\images\image21.png) 
	
**Note: For case-insensitive and wildcard searches in UIE, below settings should be included in managed-schema of the Solr core. Documents need to be recrawled after making this change.** 

```
<fieldType name="text_ws" class="solr.TextField" positionIncrementGap="100">
    <analyzer>
      <tokenizer class="solr.WhitespaceTokenizerFactory"/>
      <filter class="solr.LowerCaseFilterFactory"/>
    </analyzer>
  </fieldType>

  <field name="metadataa_s" type="text_ws" indexed="true" stored="false" multiValued="true"/>
```

Map the `metadataa_s` field to repository index fields that will be used for searching. For example: 

```
  <copyField source="d_titlea_s" dest="metadataa_s"/>
  <copyField source="d_classu_namea_s" dest="metadataa_s"/>
  <copyField source="d_repositoryu_ida_s" dest="metadataa_s"/>
```


	

### Install Lucene 

Apache Lucene™ is a high-performance, full-featured text search engine library written entirely in Java. It is a technology suitable for nearly any application that requires full-text search, especially cross-platform.

[Lucene](https://lucene.apache.org/core/documentation.html) 

## Install Discovery Services  

### Setup UIE Crawler 

- Extract the `uie-crawl` folder from the UIE installation package to a folder on the server: 

	![uie-folder](.\images\image22.png) 
	
- Locate `run-crawler.cmd` batch script under the uie-crawl installation folder and open it in edit mode. 
Check that it points to correct java installation. 
Add the following specifically for CMOD crawler:  

	```
	"%JAVA_HOME%\bin\java" -Xmx3072m -XX:MaxGCPauseMillis=200 -XX:+UseG1GC -Dloader.path=ext -Djava.library.path="C:\IBM\OnDemand\V10.1\bin;C:\IBM\OnDemand\V10.1\www;C:\IBM\OnDemand\V10.1\www\api" -jar uie-crawl-web.jar 
	```

	![uie-folder](.\images\image23.png) 
	
- Open `application.properties` file which contains the web interface ports, the administrator credentials, the embedded DB settings etc. and adjust settings if required  

	![uie-folder](.\images\image24.png) 
	
- In Command Prompt run `run-crawler.cmd` batch script placed under `uie-crawl` folder: 

	![uie-folder](.\images\image25.png) 
	
- Open browser and go to URL `http://<server>:<port>/uie/discovery`, by default is [http://localhost:8080/uie/discovery](http://localhost:8080/uie/discovery)  

- Enter default username `admin` and password (provided by request) 

	![uie-folder](.\images\image26.png) 
	
**Note: After installing crawlers for the first time, it is a good idea to restart UIE discovery crawler services.** 

### Install UIE Crawlers 

#### FileNet CE 5.2 Crawler 

- Edit `crawler_CE52_SOLR8_MSSQL_Demo.xml` file under `/uie-crawl/samples/config/ce52/solr8` folder (assuming that CE 5.2 is using MSSQL server as the database) 

- Update the following properties to match the specific environment and save the file
	- IndexManager > url = `http://<servername>:<port>/solr/SODemo` 
	- AgentLockPersistence > local folder path 
	- Database and FileNet settings to be updated as per the prerequisites mentioned above: 

	![uie-folder](.\images\image27.png) 
	![uie-folder](.\images\image28.png) 
	
- In the UIE discovery services admin console, click `Add Service`
- Enter a Service Name and Description: 

	![uie-folder](.\images\image29.png) 
	
- Under Service configuration file, select the following file under `/uie-crawl/samples/config/ce52/solr8` folder: `crawler_CE52_SOLR8_MSSQL_Demo.xml` 
- Click `Submit` 
- To start crawler, click the `Start` button 

	![uie-folder](.\images\image30.png) 
	
	If there are no errors, the status will show as Running: 
	
	![uie-folder](.\images\image31.png) 
	
- Click `Detail View` to verify the number of documents crawled and whether the crawlpoint is updated
- Stop the crawler by clicking `Stop`, after a few minutes of running
- To verify that CE5.2 index is created, login to Solr admin page using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select your SODemo core, then select `Query` to open the Query page 

	![uie-folder](.\images\image32.png) 
	
- In the Request-Handler (qt) field, enter the following default query, and click `Execute Query: /select`
- Search for the `numFound` value and verify it equals the number of documents that should have been indexed by the crawler 

	![uie-folder](.\images\image33.png) 
	
#### CMIS Alfresco crawler 

- Edit `crawler_SOLR8_MainRepository.xml` file under `/uie-crawl/samples/config/cmis10/solr8` folder
- Update the following properties to match the specific environment and save the file
	- IndexManager > url = `http://<servername>:<port>/solr/SODemo`
	- AgentLockPersistence > local folder path
	- CMIS settings to be updated as per the prerequisites mentioned above: 
	
	![uie-folder](.\images\image34.png) 
	
- In the UIE discovery services admin console, click `Add Service` and use the above config file to add a new crawler service

	![uie-folder](.\images\image35.png) 
	
- To start crawler, click the `Start` button 

	![uie-folder](.\images\image30.png) 
	
	If there are no errors, the status will show as Running: 
	
	![uie-folder](.\images\image31.png) 
	
- Click `Detail View` to verify the number of documents crawled and whether the crawlpoint is updated
- Stop the crawler by clicking `Stop`, after a few minutes of running
- To verify that CE5.2 index is created, login to Solr admin page using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select your SODemo core, then select `Query` to open the Query page 

	![uie-folder](.\images\image32.png) 
	
- In the Request-Handler (qt) field, enter the following default query, and click `Execute Query: /select`
- Search for the `numFound` value and verify it equals the number of documents that should have been indexed by the crawler 

	![uie-folder](.\images\image33.png) 
	
#### Box crawler 

- If Java 8 is used for crawler, below steps are required
	- Edit `%JAVA_HOME%/jre/lib/security/java.security` by adding `security.provider.nn=org.bouncycastle.jce.provider.BouncyCastleProvider`:

	![uie-folder](.\images\image36.png) 

	- Copy `bcprov-jdk15on-1.x.jar` and `bcpkix-jdk15on-1.x.jar` files from `/uie-crawl/plugins/uie-plugin-box-7.6.1.X` folder to `%JAVA_HOME%/jre/lib/ext` 
- Edit `crawler_Box_SOLR8.xml` file under `/uie-crawl/samples/config/box/solr8` folder
- Update the following properties to match the specific environment and save the file

	- IndexManager > url = `http://<servername>:<port>/solr/SODemo`
	- AgentLockPersistence > local folder path
	- Box settings to be updated as per the prerequisites mentioned above: 
	
	![uie-folder](.\images\image37.png) 
	
	![uie-folder](.\images\image38.png) 
	
- In the UIE discovery services admin console, click `Add Service` and use the above config file to add a new crawler service

	![uie-folder](.\images\image39.png) 
	
- To start crawler, click the `Start` button 

	![uie-folder](.\images\image30.png) 
	
	If there are no errors, the status will show as Running: 
	
	![uie-folder](.\images\image31.png) 
	
- Click `Detail View` to verify the number of documents crawled and whether the crawlpoint is updated
- Stop the crawler by clicking `Stop`, after a few minutes of running
- To verify that CE5.2 index is created, login to Solr admin page using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select your SODemo core, then select `Query` to open the Query page 

	![uie-folder](.\images\image32.png) 
	
- In the Request-Handler (qt) field, enter the following default query, and click `Execute Query: /select`
- Search for the `numFound` value and verify it equals the number of documents that should have been indexed by the crawler 

	![uie-folder](.\images\image33.png) 
	
#### CMOD crawler 

- Edit `crawler_CMOD_SOLR8_AdobePDF.xml` file under `/uie-crawl/samples/config/cmod/solr8` folder
- Update the following properties to match the specific environment and save the file

	- IndexManager > url = `http://<servername>:<port>/solr/SODemo`
	- AgentLockPersistence > local folder path
	- CMOD settings to be updated as per the prerequisites mentioned above: 

	![uie-folder](.\images\image40.png) 
	
- In the UIE discovery services admin console, click `Add Service` and use the above config file to add a new crawler service

	![uie-folder](.\images\image41.png) 
	
- To start crawler, click the `Start` button 

	![uie-folder](.\images\image30.png) 
	
	If there are no errors, the status will show as Running: 
	
	![uie-folder](.\images\image31.png) 
	
- Click `Detail View` to verify the number of documents crawled and whether the crawlpoint is updated
- Stop the crawler by clicking `Stop`, after a few minutes of running
- To verify that CE5.2 index is created, login to Solr admin page using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select your SODemo core, then select `Query` to open the Query page 

	![uie-folder](.\images\image32.png) 
	
- In the Request-Handler (qt) field, enter the following default query, and click `Execute Query: /select`
- Search for the `numFound` value and verify it equals the number of documents that should have been indexed by the crawler 

	![uie-folder](.\images\image33.png) 
	
#### CM8 crawler 

- Move `cmbicmsdk81-8.6.0.jar` and uie-plugin-cm8-7.6.1.x.jar from uie-crawl/plugins/uie-plugin-cm8-7.6.1.x folder to /uie-crawl/ext folder
- Copy cmbicmsrvs.ini from under /uie-crawl/samples/config/cm8/solr8 folder to the path specified in the CM8 crawler config
- Edit and update the CM8 server properties to match the specific environment and save the file

	![uie-folder](.\images\image42.png) 
	
- Edit `crawler_CM8_SOLR8_cm8db07.xml` file under `/uie-crawl/samples/config/cm8/solr8` folder
- Update the following properties to match the specific environment and save the file: 

	- IndexManager > url = `http://<servername>:<port>/solr/SODemo`
	- AgentLockPersistence > local folder path
	- CM8 settings to be updated as per the prerequisites mentioned above 
	
	![uie-folder](.\images\image43.png) 
	
- In the UIE discovery services admin console, click `Add Service` and use the above config file to add a new crawler service: 

	![uie-folder](.\images\image44.png) 
	
- To start crawler, click the `Start` button 

	![uie-folder](.\images\image30.png) 
	
	If there are no errors, the status will show as Running: 
	
	![uie-folder](.\images\image31.png) 
	
- Click `Detail View` to verify the number of documents crawled and whether the crawlpoint is updated
- Stop the crawler by clicking `Stop`, after a few minutes of running
- To verify that CE5.2 index is created, login to Solr admin page using `http://<servername>:<port>/solr`, by default is [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select your SODemo core, then select `Query` to open the Query page 

	![uie-folder](.\images\image32.png) 
	
- In the Request-Handler (qt) field, enter the following default query, and click `Execute Query: /select`
- Search for the `numFound` value and verify it equals the number of documents that should have been indexed by the crawler 

	![uie-folder](.\images\image33.png) 


	
	
	
	
### Update UIE Crawlers 

## Install Search Services 

### Install UIE Searcher on IBM WewbSphere 

### Install UIE Searcher on IBM WebSphere Liberty 

### Install UIE Searcher on WebLogic 

### Install UIE Searcher on WildFly JBoss 



