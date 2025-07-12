# DOCKER IN GENERAL COMMANDS

Install docker: https://docs.docker.com/engine/install/ubuntu/#upgrade-docker-after-using-the-convenience-script
```
    1  ls
    2  ls -la
    3  apt-get update
    4  docker --version
    5  Docker --version
    6  sudo sh get-docker.sh
    7  Executing docker install script, commit: 7cae5f8b0decc17d6571f9f52eb840fbc13b2737
    8  docker --version
    9  docker run -t ubuntu /bin/bash
   10  docker run -it ubuntu /bin/bash
   11  docker ps -a
   12  docker run -it --name db-server ubuntu /bin/bash
   13  docker ps -a
   14  docker run -it --name tomcat-server -h tomcat-server ubuntu /bin/bash
   15  docker rm db-server
   16  docker ps -a
   17  docker ps -a -q
   18  docker rm `docker ps -a -q`
   19  docker ps -a
   20  docker run -t -d ubuntu /bin/bash
   21  docker ps -a
   22  history
```

# CREATING DOCKER IMAGE USING 2 METHODS

# 1) COMMIT METHOD

```
ngnix default port :80
Go to this website and copy installation commands: https://docs.docker.com/engine/install/ubuntu/#upgrade-docker-after-using-the-convenience-script
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
{1st we will create docker image and install nginx inside it and create image out of it}
docker run -it ubuntu /bin/bash {creating docker container}
{inside container}-
apt-get update
apt-get install nginx
apt-get install vim (asia , 44)
cd /var/www/html
vim index.html
---
<html>
        <body>
                <h1 style="color:red;"> Hello world </h1>
        </body>
</html>
--
exit
(ur vm): docker commit <imageid> <docker id>/<name u want to give>
docker commit 85cd54baeab4 ankitakumari346/ankita-image -----> commit method
docker images
docker run -it -p 8080:80 --name ngnix-cont ankitakumari346/ankita-image /bin/bash
{now u r inside the container}
{now u need to start nginx just like tomcat}
root@2d2328f74a18:/# service nginx status
 * nginx is not running
root@2d2328f74a18:/# service nginx start 
 * Starting nginx nginx
{also enable the port 8080 on security list } -> tcp , port 8080 , 0.0.0.0/0
go to url: http://44.201.123.244:8080/ {public ip of the server}
```

# 2) BUILD METHOD 

```
root@2d2328f74a18:/var/www# exit
exit
root@ip-172-31-90-252:~# ls
get-docker.sh  snap
root@ip-172-31-90-252:~# mkdir images
root@ip-172-31-90-252:~# cd images/
root@ip-172-31-90-252:~/images# vim Dockerfile 
root@ip-172-31-90-252:~/images# cat Dockerfile 
----
FROM ubuntu:20.04       
RUN apt-get update
RUN apt-get install -y nginx
COPY index.html /var/www/html
ENTRYPOINT service nginx start && bash
-----
root@ip-172-31-90-252:~/images# 
root@ip-172-31-90-252:~/images# cd ..
root@ip-172-31-90-252:~# ls
get-docker.sh  images  snap
root@ip-172-31-90-252:~# cd images/
root@ip-172-31-90-252:~/images# vi index.html
---
<html>
        <body>
                <h1 style="color:red;">Docker image 2nd method </h1>
        </body>
</html>
--
root@ip-172-31-90-252:~/images# ls
Dockerfile  index.html
root@ip-172-31-90-252:~/images# 
{now run this dockerfile , the below command will run "Dockerfile" file and build a image named "second-method-image" and copy everything like here (index.html) using "." i.e present directory content to the image build}
root@ip-172-31-90-252:~/images# docker build -t "ankitakumari346/second-method-image" .
root@ip-172-31-90-252:~/images# docker images
REPOSITORY                            TAG       IMAGE ID       CREATED          SIZE
ankitakumari346/second-method-image   latest    6a34bf56a63f   5 minutes ago    190MB
ankitakumari346/ankita-image          latest    cccbbaeafae8   34 minutes ago   207MB
ubuntu                                latest    f9248aac10f2   2 weeks ago      78.1MB
root@ip-172-31-90-252:~/images# docker run -it -p 8080:80 --name ngnix-second ankitakumari346/second-method-image /bin/bash  [u r creating a container from the above created image]
 * Starting nginx nginx                                                                                                           [ OK ] 
root@8d0ea4f8341d:/#  
 {url: http://44.201.123.244:8080/}
root@8d0ea4f8341d:/# exit                           
exit
root@ip-172-31-90-252:~/images# docker login -u ankitakumari346  [here u r login in to docker]
root@ip-172-31-90-252:~/images# docker push ankitakumari346/second-method-image
{now the above code has created docker image and pushed on docker-hub}
```

# -CREATING DOCKER IMAGE FOR TESTING SERVER FROM JENKINS FIRST SERVER ON WHICH WE CREATE .WAR FILE

```
root@ip-172-31-90-252:~# ls
get-docker.sh  images  snap
root@ip-172-31-90-252:~# mkdir source
root@ip-172-31-90-252:~# cd source/
root@ip-172-31-90-252:~/source# git clone https://github.com/prasad-gamut/gamutkart2.git
root@ip-172-31-90-252:~/source# cd gamutkart2/
root@ip-172-31-90-252:~/source/gamutkart2# ls
Docker                   Jenkinsfile_Parameterized    apache-tomcat-9.0.85.tar.gz  multi-deploy-IPs.txt         target
Dockerfile               Jenkinsfile_k8s              chef                         multi_deploy.sh              test.sh
Dockerfile_usr           Jenkinsfile_slave            cookbooks                    pipeline-code-promotion.zip
Jenkins                  K8S                          create-env.sh                pom.xml
Jenkins_Chef_Deployment  apache-tomcat-8.5.38.tar.gz  create_lb.sh                 roles
Jenkinsfile              apache-tomcat-9.0.85         deploy.sh                    src

root@ip-172-31-90-252:~/source/gamutkart2# cat Dockerfile
-------
FROM ubuntu:20.04 
RUN apt-get update
RUN apt-get install -y openjdk-11-jdk
ENV JAVA_HOME /usr
ADD apache-tomcat-9.0.85.tar.gz /root
COPY target/gamutkart.war /root/apache-tomcat-9.0.85/webapps
ENTRYPOINT /root/apache-tomcat-9.0.85/bin/startup.sh && bash
-----------
root@ip-172-31-90-252:~/source/gamutkart2# docker build -t "ankitakumari346/gamutkart-img-airtel" .
root@ip-172-31-90-252:~/source/gamutkart2# docker images
REPOSITORY                             TAG       IMAGE ID       CREATED             SIZE
ankitakumari346/gamutkart-img-airtel   latest    391540fa93ef   54 seconds ago      681MB
ankitakumari346/second-method-image    latest    6a34bf56a63f   31 minutes ago      190MB
ankitakumari346/ankita-image           latest    cccbbaeafae8   About an hour ago   207MB
ubuntu                                 latest    f9248aac10f2   2 weeks ago         78.1MB
root@ip-172-31-90-252:~/source/gamutkart2# docker run -it -p 8080:8080 --name gamut-container ankitakumari346/gamutkart-img-airtel /bin/bash
{above we have written 8080 becoz it is tomcat not nginx}
URL: http://44.201.123.244:8080/gamutkart/
docker u login ankitakumari346
root@ip-172-31-90-252:~# docker push ankitakumari346/gamutkart-img-airtel
```
