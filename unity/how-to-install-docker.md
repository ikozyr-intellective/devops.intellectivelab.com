## How To Install Docker

- Check if the Virtualization is enabled on your laptop

![virtualization-enabled](how-to-install-docker-1.png)

### Configure Unity Docker Image

**Configuration**

- Create folder on the drive you shared to store Open Liberty & Unity configuration files, `C:\DockerUnity` for example
- Place `server.xml` to this folder
- Open cmd or PowerShell in admin mode and navigate to this folder, running command 
 `cd C:\DockerUnity\`
- Run `docker login docker.devops.intellectivelab.com`

	> Username: `serviceaccount`  
	> Password: `V3ga123456`

- You should get the message `login succeeded`
- Run `docker run -it -v c:/DockerUnity:/opt/vu  -p 9080:9080  --rm docker.devops.intellectivelab.com/unity-classic/unity-7:7.6.1-ol`, here `c:/DockerUnity` is a folder you copied `server.xml`

It will check if the docker image is available locally.
If not then it will be downloaded automatically.
For the first time, it will take some time to download the image.
Once downloaded, the local unity instance will be up & running. 

**Access the Unity**

- Connect to Intellective VPN
- Make sure the FileNet environment is up and accessible. 

	Hit [http://172.31.27.3:9080/wsi/FNCEWS40MTOM/](http://172.31.27.3:9080/wsi/FNCEWS40MTOM/) to confirm.

- Hit [http://localhost:9080/vu](http://localhost:9080/vu) to access the Unity

	> Username: `p8admin_demo`  
	> Password: `V3ga123456`
	
- Wizard should guide you through establishing connection to FileNet. Use the endpoint to connect to Filenet [http://172.31.27.3:9080/wsi/FNCEWS40MTOM/](http://172.31.27.3:9080/wsi/FNCEWS40MTOM/)
	
	
## Contents

**Containers**

[Unity on Docker](./unity/unity-on-docker.md): the step-by-step guide how to run Unity 7 in Docker environment.

[Unity on Kubernetes](./unity/unity-on-kubernetes.md): a comprehensive guide how to run Unity 7 on Kubernetes using Istio service mesh.

**Environment Management**

[Overview](./envmgmt/overview.md): GitOps approach implemented using Terraform, Helm, AWS EC2

## Contacts

[Contact Intellective](https://www.intellective.com/contact-us/)