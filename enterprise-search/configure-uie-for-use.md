# Configure UIE for Use   

## Contents 

[Post-Installation Configuration](./configure-uie-for-use.html#post-installation-configuration)  
- [Configure UIE crawler and Solr as services](./configure-uie-for-use.html#configure-uie-crawler-and-solr-as-services)  
- [Clear UIE Solr Index](./configure-uie-for-use.html#clear-uie-solr-index) 

[UIE Crawler Logs](./configure-uie-for-use.html#uie-crawler-logs)  
	
[Configure Unity for UIE](./configure-uie-for-use.html#configure-unity-for-uie)  
- [Prerequisites in WebSphere](./configure-uie-for-use.html#prerequisites-in-websphere)  
- [Configure Datasource and Connector](./configure-uie-for-use.html#configure-datasource-and-connector)  
- [Configure Content Lists](./configure-uie-for-use.html#configure-content-lists)  
- [Configure Search Template](./configure-uie-for-use.html#configure-search-template)  
- [Configure Tab and assign search template](./configure-uie-for-use.html#configure-tab-and-assign-search-template)  
- [UIE Search Test](./configure-uie-for-use.html#uie-search-test) 

## Post-Installation Configuration   

### Configure UIE crawler and Solr as services 

#### Windows 

Solr and Crawler scripts can be installed as Windows services. To install these as services, download [NSSM](https://nssm.cc/download) and extract files to a folder. 
**Note**: Do not delete `nssm.exe`, since it will be used when services need to be uninstalled in the future. 

Install Solr as a service:

- In Command Prompt, go to the folder where `nssm.exe` is located (e.g `C:\Program Files\nssm\win64\nssm`) and type `nssm install solr-8.5.1` 

- Select the path to the `solr.cmd` folder and enter the arguments as shown: 
 
	![uie-folder](.\images\image65.png) 

- On Details tab, enter the information below and click `Install service`: 

	![uie-folder](.\images\image66.png) 

Install Crawler as a service
- In Command Prompt, go to the folder where `nssm.exe` is located (e.g `C:\Program Files\nssm\win64\nssm`) and type `nssm install crawler-7.6.1`
- Select the path to the `run-crawler.cmd` folder:
	
	![uie-folder](.\images\image67.png) 
	
- On Details tab, enter the below information and click `Install service`:
	
	![uie-folder](.\images\image68.png) 
	
- Verify that the services have been installed in Windows Services:
	
	![uie-folder](.\images\image69.png) 
	
To remove Solr and Crawler services: 
- In Command Prompt, go to the folder where `nssm.exe` is located (e.g `C:\Program Files\nssm\win64\nssm`) and type `nssm remove solr-8.5.1`
- Click `Yes` to remove the service
- Type `nssm remove crawler-7.6.1`
- Click `Yes` to remove the service
- Verify that the services have been removed in Windows Services 

#### Linux 

### Clear UIE Solr Index 

If there is a need to re-crawl the repositories after an index schema change, first clear the Solr index using the instructions in this section, then run the crawler again as discussed in the previous section.
- Edit `solrconfig.xml` for SODemo under `<solr_install>/server/solr/SODemo/conf` 
	
	![uie-folder](.\images\image70.png) 
	
- Search for `enableRemoteStreaming` and update the settings as shown below (add `enableStreamBody` attribute if not already present) 
	
	![uie-folder](.\images\image71.png) 
	
- Restart Solr by stopping and starting the service
- Log into the Solr Admin page using `http://<servername>:<port>/solr`, by default [http://localhost:8983/solr](http://localhost:8983/solr) 
- From the Core Selector drop-down, select `SODemo` core, then select `Query` to open the Query page
- In the `Request-Handler (qt)` field, enter `/update` 
- In the Raw Query Parameters field, enter `stream.body=<delete><query>*:*</query></delete>&commit=true` 
	
	![uie-folder](.\images\image72.png) 
	
- Click `Execute Query`
- From the left-side menu bar, select `Core Admin`
- From the Core Selector drop-down, select `SODemo` core, then click `Reload`. The `numDocs` value should display 0 indicating there are no documents in the index anymore: 

	![uie-folder](.\images\image73.png) 
	
- From the Core Selector drop-down, select `SODemo` core, then select `Query` to open the Query page
- In the `Request-Handler (qt)` field, enter `/select`
- In the `q` field, enter `*:*`
- Click `Execute Query`
- Search for the `numFound` value and verify it is 0: 

	![uie-folder](.\images\image74.png) 
	
## UIE Crawler Logs 

Crawler log files below are located, by default, under `/uie-crawl/logs`:
- `error.log`
- `debug.log`

To change logging level, edit `logback.xml` under `/uie-crawl/ext` folder and change logging at the root level. Restart UIE crawler service. 
	
![uie-folder](.\images\image75.png) 
	
## Configure Unity for UIE 
	
### Prerequisites in WebSphere 

#### JDBC providers 

Verify all required database JDBC providers are configured in WebSphere:
	
![uie-folder](.\images\image76.png) 
	
#### CMOD Document Search 

- In WebSphere admin console, go to `Environment > Shared libraries`: 
	
	![uie-folder](.\images\image77.png) 
	
- Create a new shared library to include `ODApi.jar` in classpath: 
	
	![uie-folder](.\images\image78.png) 
	
- Go to the installed Unity application and click `Shared library references`: 
	
	![uie-folder](.\images\image79.png) 
	
- Reference the newly created shared library: 
	
	![uie-folder](.\images\image80.png) 
	
#### Box Document Search 

- In WebSphere admin console, go to `Environment > Naming > Name space bindings`
	
	![uie-folder](.\images\image81.png) 
	
- Create a new binding for searching Box documents with the information below:
	- Binding Type: `String`
	- Binding Identifier: `box/appSettings`
	- Name in name space relative to lookup name prefix 'cell/persistent/': `box/appSettings` 
	- String value: copy the entire text from RSA Key Pair JSON file created above 
	
		![uie-folder](.\images\image82.png) 
		
- Restart WebSphere 

#### Configure Datasource and Connector 

- Login to Unity `http://<servername>:<port>/<contextroot>`, by default [http://localhost:9080/vu](http://localhost:9080/vu) using admin username, password is available by request
- Go to Configuration Console tab and `Global > System > Connectors` and create a UIE connector: 
	
	ID: `uie_provider` 
	
	Type: `Unity Intelligence Engine` 
	
	![uie-folder](.\images\image83.png) 
	
- On Parameters tab, select Operators Set `Unity` (or the appropriate operators set available): 

	![uie-folder](.\images\image84.png) 
	
- On Properties mapping tab, enter the following mapping: 
	
	![uie-folder](.\images\image85.png) 

- On Additional properties tab, enter the following: 

	![uie-folder](.\images\image86.png) 
	
**Note**: Index group need to be added from XML configuration once the connector is created under `<RepositoryDataProvider ID="uie_provider">` node as shown below and should match exactly with the one configured in crawler configurations: 
	
![uie-folder](.\images\image87.png) 
	
- On Custom section tab, add two new sections: 
 
	- Communication
	
		Enter the following in Custom Section XML:  
	
		```xml 
		<Communication>
			<EncryptionKeyId>3DES</EncryptionKeyId>
		</Communication>
		``` 
		![uie-folder](.\images\image88.png) 
		
	- ViewerProperties 
	
		Enter the following in Custom Section XML, and Repositary internal name (`CE52` here) should match the repository ID configured in crawler config:
		
		```xml
		<ViewerProperties>
			<Repository internal="CE52"> 
				<DefineProperties>
					<Property ID="repositoryType" value="cpe_provider"/>
					<Property ID="documentId.Id" value="{This.document_id@s}"/>
					<Property ID="documentId.$os" value="${solution.object_store}"/>
				</DefineProperties>
			</Repository>
			<Repository internal="CMIS">
				<DefineProperties>
					<Property ID="repositoryType" value="cmis_alfresco_provider"/>
					<Property ID="documentId.cmis:objectId" value="{This.Id}"/>
				</DefineProperties>
			</Repository>
			<Repository internal="CmodRepository">
				<DefineProperties>
					<Property ID="repositoryType" value="cmod_provider"/>
					<Property ID="documentId.Id" value="{This.Id}"/>
				</DefineProperties>
			</Repository>
			<Repository internal="Cm8Repository">
				<DefineProperties>
					<Property ID="repositoryType" value="cm8_provider"/>
					<Property ID="documentId.cmis:objectId" value="{This.Id}"/>
					<Property ID="documentId.Id" value="{This.Id}"/>
				</DefineProperties>
			</Repository>
			<Repository internal="BoxRepository">
				<DefineProperties>
					<Property ID="documentId.Id" value="{This.$id}"/>
					<Property ID="repositoryType" value="box_provider"/>
				</DefineProperties>
			</Repository>
		</ViewerProperties>
		``` 
		
#### Configure	Content Lists 


	
	![uie-folder](.\images\image89.png)
	![uie-folder](.\images\image90.png)
	![uie-folder](.\images\image91.png)
	![uie-folder](.\images\image92.png)
	![uie-folder](.\images\image93.png)
	![uie-folder](.\images\image94.png)
	![uie-folder](.\images\image95.png)
	![uie-folder](.\images\image96.png)
	![uie-folder](.\images\image97.png)
	![uie-folder](.\images\image98.png)
	![uie-folder](.\images\image99.png)
	![uie-folder](.\images\image100.png)
	![uie-folder](.\images\image101.png)
	![uie-folder](.\images\image102.png)
	








### Configure Datasource and Connector 

### Configure Content Lists 

### Configure Search Template 

### Configure Tab and assign Search Template 

### UIE Search Test 

## Crawler configuration XML 


