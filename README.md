# Jenkins_Docker_Kubernetes
Jenkins_docker_kubernetes

<img width="957" height="452" alt="devops" src="https://github.com/user-attachments/assets/cfa23297-e780-40db-8e27-718d3b111da4" />


Now lets start from beginning  
### MANUAL  
- First we created 2 servers: Devops and then testing server.
- We pulled the code from github and pulled on devops server ,installed Git , Maven on the server and build the code using `mvn install` and pom.xml present inside the repo.
- Now the build code i.e srtech.war was there inside target/srtech.war.
- Now we went to testing server and installed tomcat , java and inbound rule on the server.
- We copied target/srtech.war i.e war file from devops server to the testing server inside /root/tomcat/webapps/
- Then on testing server we went to /root/tomcat/bin and started tomcat i.e ./startup.sh and went to the url http://<public ip>:8080/srtech.
- To overcome this manual process of building and copying the code , Jenkins came into the picture.
    
### JENKINS   
- Now instead of devops server and testing server, we created a jenkins server and testing server.
- On jenkins server , we installed java (becoz tomcat is in java language), maven ,created distro folder , inside it installed tomcat (to run jenkins server) , inside tomcat/webapps we installed jenkins.war and then went inside /distro/apache-tomcat/bin and started tomcat ./startup.sh.
- Then we started jenkins console , build job inside it (like srtech) , inside the job we wrote SCM -> git and gave the github link , credentials.
- Inside the build section we chose maven and wrote "install". And then build code.
- On the jenkins server , we checked inside /.jenkins/workspace , srtech is present and inside /srtech/target , .war file is present so build is done automatically.
- We went to testing server , installed openjdk , mkdir distros ,cd distros , installed apache-tomcat.
- Now again we went to the job and removed the maven build and chose execute shell and wrote the copy script i.e copy .war file from target/srtech.war to /root/distro/apache-tomcat/webapps/.
- Build the job again and now we can see on testing server , the .war file is present so we can start tomcat there apply inbound rules and start the application.
- Now with the help of jenkins we can also pull the code from github periodically whenevr a new change comes using : Poll scm and github hook (webhook).
  
### JENKINS CI/CD PIPELINES  
- Also with the help of jenkins we  created manual 3 freestyle jobs and created manual pipeline ( clone , build and deploy) and also used automatic pipeline script using pipeline job.
- Rather than writing pipeline script , devops engineer can write the jenkins file inside github itself and then we can pull it using "Pipeline script from SCP".
  
### DOCKER  
- Now rather than installing tomcat ,jdk and other tools again and again on testing , jenkins server. We can create container and images.
- lets take example of gamutkart, We created a vm clones gamutkart2.git and inside it a Dockerfile present which is going to create an image from ubuntu , install jdk , copy apache-tomcat.tar.gz frm repo to image's /root/, copy the target/gamutkart.war from the repo to the image's /root/apache-tomcat/webapps and then start the tomcat.
- So once we build the image using that docker file , now in the image we have jdk, apache-tomcat , .war file and tomcat will start automatically once any container is build from that image.
- So once jenkin's build the code i.e /target/.war file , we can create a image from it and then we can create any number of containers for testing purposes using dockerfile and no need to  install jdk, tomcat,copying .war file again and again.
