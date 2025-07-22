<img width="1920" height="646" alt="image" src="https://github.com/user-attachments/assets/1b9ace81-0b6f-461d-b2c6-8c0d97ff7fa4" /># Azure CI-CD Pipeline

- Azure devops organisation -> my azure devops organisation -> give credentials
   
  <img width="1916" height="1022" alt="image" src="https://github.com/user-attachments/assets/3ab79b91-6419-4590-817c-e1940bcc6592" />

  <img width="1917" height="1015" alt="image" src="https://github.com/user-attachments/assets/71fde175-d65a-4609-a589-6b2c177460aa" />

- Create new organisation -> continue.

  <img width="1916" height="1016" alt="image" src="https://github.com/user-attachments/assets/1093677e-8885-483e-a656-e9f6293ec172" />

  <img width="1916" height="1016" alt="image" src="https://github.com/user-attachments/assets/397fb933-dd4d-486b-82f2-b217807e6d79" />

  <img width="1060" height="962" alt="image" src="https://github.com/user-attachments/assets/65e23d47-be72-4cd1-84c4-7aeb756bf67b" />

- Give a project name -> keep private -> create project

  <img width="1920" height="816" alt="image" src="https://github.com/user-attachments/assets/1373afa1-2f2e-489d-b50a-5c547f464b63" />

- Now you are inside your azure devops
- Here we will have azure boards , repos , pipelines , test plans , artificats
  
  <img width="1920" height="820" alt="image" src="https://github.com/user-attachments/assets/ce8bc20c-c8b1-41f5-9b5e-da17bf9e1501" />
 
- Go to repo:

  <img width="1899" height="961" alt="image" src="https://github.com/user-attachments/assets/a4ab1bef-670a-41ea-b0bf-afdec3710e53" />

- Now our code is present in your github repo , But here we have azure repo so we need to give github repo link to azure:-> Repo-> Import -> write your github repo link  
  
   <img width="1917" height="1013" alt="image" src="https://github.com/user-attachments/assets/160937f9-c109-4125-979f-5a33d3b25512" />

- Now it has cloned the repo:  

  <img width="1920" height="1028" alt="image" src="https://github.com/user-attachments/assets/4c35b1e3-2906-4a7a-a31f-57d8c3549721" />

- Use the branch in which your code is present :  

  <img width="1919" height="627" alt="image" src="https://github.com/user-attachments/assets/d303f324-6436-420b-ae0a-3af95d7f4633" />

- click on pipelines -> create pipeline:  

  <img width="1920" height="776" alt="image" src="https://github.com/user-attachments/assets/03d4556e-624a-470b-8c89-294f02df2ca0" />

- Select azure repo:  

  <img width="1920" height="802" alt="image" src="https://github.com/user-attachments/assets/71bcd753-0734-418a-9210-a749d1c3ccb6" />

- Here we are going to use pipeline to :
- 1. Build docker image
- 2. Push image to docker-hub (registery)
- Select docker (2nd one):

  <img width="1919" height="998" alt="image" src="https://github.com/user-attachments/assets/a8168067-4670-47b5-bbe2-229df4f276e9" />

  <img width="1913" height="1013" alt="image" src="https://github.com/user-attachments/assets/9be255e4-6ef3-4438-8b44-72f482ce1401" />

- Go to your Azure console: search container registeries: -> Create:

  <img width="1917" height="1013" alt="image" src="https://github.com/user-attachments/assets/699da817-c00d-43cc-ab45-3e6de4faa053" />

- Enter details(default resource grp) - > review and create

  <img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/4e57d93b-eb76-4516-8c2c-8c58f34fb48c" />

  <img width="1919" height="847" alt="image" src="https://github.com/user-attachments/assets/9243c59f-af2a-4880-8012-35c94a633227" />

- In devops , we had docker hub / registery but here we are having in azure devops-> ContainerRegistery
- Now Go back to your azure devops page and now u will be seeing your container registery:

  <img width="1903" height="1067" alt="image" src="https://github.com/user-attachments/assets/a493990f-a554-45fd-91c4-2b5480d2cfb6" />

  <img width="1920" height="997" alt="image" src="https://github.com/user-attachments/assets/6561af5d-d988-4a2f-81d0-585803ec1ca1" />

- Now click on validate:

  <img width="1916" height="908" alt="image" src="https://github.com/user-attachments/assets/6bc3f237-84bb-4e52-a539-c957361984c5" />

- Now we will create one agent becoz when we create an image from vm , it takes by default but here we will be needing agent for that.
- Remove main -> select paths -> include - > result/* (like gamutkart)
```
trigger:
 paths:
   include:
     - result/*

```

  <img width="1920" height="1025" alt="image" src="https://github.com/user-attachments/assets/1f592852-09bb-478f-9d79-f3b1d5db3dd1" />

  <img width="1920" height="1200" alt="Screenshot (1908)" src="https://github.com/user-attachments/assets/69e3dcaa-9bcc-406f-b6ad-fe3d7326dd34" />

   <img width="1920" height="434" alt="image" src="https://github.com/user-attachments/assets/7fd8b5d3-53d9-4658-9c6c-2b83b282986e" />

- Here self becoz it will take repo from self repo
- in dockerfilepath : your deockerfile path but if u r taking image from docker-hub then u need to give ur credentials too.
- Here we r taking image from azure registery

  <img width="1920" height="646" alt="image" src="https://github.com/user-attachments/assets/93630c4c-5fb7-4e24-a19f-70810241f887" />

- Remove pool

  <img width="1920" height="915" alt="image" src="https://github.com/user-attachments/assets/fd078b4f-e953-4835-9764-b26cca8bf6b9" />

- current yaml:
```
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
   include:
     - result/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '18aea5db-c9ef-4cae-9d83-ad2bad91ae17'
  imageRepository: 'airtelankita'
  containerRegistry: 'airtelankitaregistery.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
  vmImageName: 'ubuntu-latest'

stages:
- stage: Build
  displayName: Build and push stage
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        command: buildAndPush
        repository: $(imageRepository)
        dockerfile: $(dockerfilePath)
        containerRegistry: $(dockerRegistryServiceConnection)
        tags: |
          $(tag)
```
- go to setting - > select your repo - > add

  <img width="1918" height="887" alt="image" src="https://github.com/user-attachments/assets/e49f53bb-0fcd-49eb-be59-ee287f00c681" />

- Write "result" here:

  <img width="1904" height="830" alt="image" src="https://github.com/user-attachments/assets/05019c92-e2bb-46c1-8662-fba3401b29a0" />

- command: build

   <img width="1920" height="552" alt="image" src="https://github.com/user-attachments/assets/76fce55f-8ee3-49ec-86ac-ed248d1f8075" />


- Overall pipeline code (yaml)
```
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
 paths:
   include:
     - result/*

resources:
- repo: self

variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '18aea5db-c9ef-4cae-9d83-ad2bad91ae17'
  imageRepository: 'airtelankita'
  containerRegistry: 'airtelankitaregistery.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'

  # Agent VM image name
  vmImageName: 'ubuntu-latest'

stages:
- stage: Build
  displayName: Build and push stage
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        containerRegistry: 'AirtelAnkitaRegistery'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
```
- Now till now our build stage code is ready
- We will create push stage pipeline also
- copy build stage code block and paste at the bottom
- Replace all build words with push
```
- stage: Push
  displayName: Push
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: Push an image
      inputs:
        containerRegistry: 'AirtelAnkitaRegistery'
        repository: '$(imageRepository)'
        command: 'push'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
```
- Click on settings of push and ensure the command is push:

  <img width="1920" height="800" alt="image" src="https://github.com/user-attachments/assets/1b48d08e-5921-489d-ada6-317224de554f" />

- Now write the below code for agent
- Also we need agent here to push the image to our container-registery
- for the agent , we need to create one vm

   <img width="1920" height="725" alt="image" src="https://github.com/user-attachments/assets/bc0f4878-2919-43f6-8ee9-7dd0e4f957ae" />

- Now final pipeline yaml code:
```
# Docker
# Build and push an image to Azure Container Registry
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker
 
trigger:
 paths:
   include:
     - result/*
 
resources:
- repo: self
 
variables:
  # Container registry service connection established during pipeline creation
  dockerRegistryServiceConnection: '18aea5db-c9ef-4cae-9d83-ad2bad91ae17'
  imageRepository: 'airtelankita'
  containerRegistry: 'airtelankitaregistery.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/result/Dockerfile'
  tag: '$(Build.BuildId)'
 
  # Agent VM image name
  pool:
    name: 'agent1'
 
stages:
- stage: Build
  displayName: Build
  jobs:
  - job: Build
    displayName: Build
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        containerRegistry: 'AirtelAnkitaRegistery'
        repository: '$(imageRepository)'
        command: 'build'
        Dockerfile: 'result/Dockerfile'
        tags: '$(tag)'
 
- stage: Push
  displayName: Push
  jobs:
  - job: Push
    displayName: Push
    steps:
    - task: Docker@2
      displayName: Push an image
      inputs:
        containerRegistry: 'AirtelAnkitaRegistery'
        repository: '$(imageRepository)'
        command: 'push'
        tags: '$(tag)'
```
  
- Go to azure console
- Create one vm : name- agent1
- select your default resource grp

  <img width="1920" height="934" alt="image" src="https://github.com/user-attachments/assets/628381f7-10c3-4483-bc7b-17e940c54ec9" />

- go to your Azure devops
- Go to agent
     
  <img width="1919" height="949" alt="image" src="https://github.com/user-attachments/assets/dd37bfbd-c57d-4186-b99e-24c5d8bb53fe" />

- Add pool

  <img width="1920" height="942" alt="image" src="https://github.com/user-attachments/assets/d3e21db9-be39-4d27-9507-3566b16c130b" />

- add pool - > self hosted - > name : agent1

  <img width="1919" height="995" alt="image" src="https://github.com/user-attachments/assets/47088dc7-aa03-4a81-9a0f-c9eb8285732c" />

  <img width="1919" height="995" alt="image" src="https://github.com/user-attachments/assets/df8f9c5e-dfd8-4568-9571-08c9a83172e4" />

- inside your pool - > click create agent - > new agent

  <img width="1915" height="1021" alt="image" src="https://github.com/user-attachments/assets/c68e5da7-3e29-485a-a9ca-e0a1dedf7b80" />

- click linux and copy the codes and run on the new vm (agent1) which you created.

  <img width="1914" height="1034" alt="image" src="https://github.com/user-attachments/assets/1a93f824-e1d4-4afb-adb4-316231e8a15f" />

- Commands to run on server:
```
~/$ mkdir myagent && cd myagent
wget https://download.agent.dev.azure.com/agent/4.258.1/vsts-agent-linux-x64-4.258.1.tar.gz
tar zxvf vsts-agent-linux-x64-4.258.1.tar.gz
./config.sh
```

<img width="1357" height="769" alt="image" src="https://github.com/user-attachments/assets/0913aa47-d3ec-4640-81b2-802dc6427db1" />

- Go to : https://learn.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops
- Agent and pools -> Self-hosted Linux agents
- On the linux agents page -> copy server url - > https://dev.azure.com/{your-organization}
- Replace with your name:- > https://dev.azure.com/azuser3774mmllocal

  <img width="1920" height="545" alt="image" src="https://github.com/user-attachments/assets/9f9eb7fb-5399-44ed-95b3-7f47b32bef76" />

- Go back to your server and write in server url:

  <img width="1351" height="770" alt="image" src="https://github.com/user-attachments/assets/b90d35aa-dcad-41ed-920c-99cb11371a05" />

- For PAT (personal access token):
- Go to your azure devops

  <img width="1920" height="1200" alt="Screenshot (1909)" src="https://github.com/user-attachments/assets/9c9495e9-9b12-42c7-ad17-69aa16571ee6" />

  <img width="1920" height="1200" alt="Screenshot (1910)" src="https://github.com/user-attachments/assets/9da361a6-b79a-423e-9ef8-46818b2568e5" />
    
  <img width="1901" height="1020" alt="image" src="https://github.com/user-attachments/assets/2020b3a3-16e2-46c6-a264-644c4ad28c9f" />

- Copy token and first enter and then paste to server

  <img width="1908" height="1021" alt="image" src="https://github.com/user-attachments/assets/f9b14652-18a6-40ab-bab3-bb0adf6188a3" />

- Enter agent pool name : in the yaml code you had written inside pool block -> "agent1"

  <img width="1351" height="471" alt="image" src="https://github.com/user-attachments/assets/65bcafd6-6ab4-416a-b288-76045fb58a7c" />

 - Now run : `./run`

   <img width="1353" height="770" alt="image" src="https://github.com/user-attachments/assets/01af3ab6-ed43-43aa-8663-8233e08af70d" />

- Now you can see , the agent is online after running `./run.sh`

  <img width="1920" height="537" alt="image" src="https://github.com/user-attachments/assets/3036806c-e431-4abf-88fb-af7c9bba5d3b" />

- Now go back to your code(pipeline yaml) and run and validate:

  <img width="1911" height="1035" alt="image" src="https://github.com/user-attachments/assets/d2de4c28-0b44-47f4-8163-a4b98d71ac63" />

- Now it is running the code:

  <img width="1917" height="862" alt="image" src="https://github.com/user-attachments/assets/c3220872-3c9e-4e7c-b308-49ef382cc427" />

- Now you will face a lot of errors : first is due to absence of docker so install docker on the server.
- On the server install docker (u can do it using azureuser)
- and then run command to give rights to azureuser (i.e put azureuser in docker group so that it can run docker commands becoz u installed docker using azureuser and not using root)
```
 sudo usermod -aG docker azureuser
 sudo systemctl restart docker
 cd ~/myagent
./svc.sh restart
exit
(again login and run)
docker ps
```
  
  <img width="1366" height="558" alt="image" src="https://github.com/user-attachments/assets/e9d43d7d-189b-47f9-874e-dcec7568506a" />

- Now on the server run below commands to start the offline agent and make it online
```
cd myagent/
./run.sh
```
- Now again go to your pipeline and rerun the pipeline and now it will work fine.

  <img width="1920" height="955" alt="image" src="https://github.com/user-attachments/assets/afbd844a-2777-4b9d-abd7-f7f917d463ae" />

   <img width="1354" height="341" alt="image" src="https://github.com/user-attachments/assets/eb9158b9-ea56-4fdc-8f16-8a1d863dfb73" />


- Now go to your azure cloud console and search container registery
- Now here u will see your image

  <img width="1920" height="580" alt="image" src="https://github.com/user-attachments/assets/7ed40793-ef4d-4f9e-be89-7c903c66c3ab" />

  <img width="1920" height="756" alt="image" src="https://github.com/user-attachments/assets/05387396-edbe-4bcf-807a-8fa2eb9f98f5" />

- Now the the pipeline is successful.
- Now go to your server - > install git -> clone the code and run docker compose command -> add ingress rule on port 8080
```
sudo su -
apt-get install git
git clone https://github.com/ankitakumari11/voting-app.git
cd <cloned folder>
docker compose
```
- Now go to url: http://<public-ip>:8080 -> http://20.244.10.96:8080/

  <img width="1920" height="534" alt="image" src="https://github.com/user-attachments/assets/41995ffc-888d-4f4c-ab57-c191804a9c2e" />



  











