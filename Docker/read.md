# DOCKER

Launch an ec2 instance (t2-medium , ubuntu)  
download docker from : https://docs.docker.com/engine/install/ubuntu/  
  
```
apt -get update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
docker --version
```

To create a container and go inside it:  
`docker run -it ubuntu /bin/bash`

 <img width="1318" height="233" alt="image" src="https://github.com/user-attachments/assets/b969d946-ebe5-49a3-8127-06ed08350620" />

To see the containers:  
`docker ps -a`

<img width="1581" height="114" alt="image" src="https://github.com/user-attachments/assets/3d53c13a-2e6f-4510-a5d4-7e3d8f210cb5" />

To give name to the container :  
`docker run -it --name ank_cont ubuntu /bin/bash`

<img width="1575" height="384" alt="image" src="https://github.com/user-attachments/assets/4909eb8a-2950-4c71-ac1a-cb3a91163940" />

If you want to set hostname of the container also then use `-h`
`docker run -it --name ank_cont2 -h ank_Cont ubuntu /bin/bash`

<img width="1239" height="87" alt="image" src="https://github.com/user-attachments/assets/4025095f-fd3e-42a8-a509-e10fb3c1db9b" />

If you want to go inside container , use : `docker attach`

<img width="1659" height="299" alt="image" src="https://github.com/user-attachments/assets/fa41836f-4b8b-4e78-ac2f-bc4245e85892" />

If you want to start as well as go inside container:  
`docker start -ai 9fef652a5079`  

<img width="857" height="79" alt="image" src="https://github.com/user-attachments/assets/260c14c2-b3f0-4a5d-8ddb-8138f8e53c63" />

If you want to execute commands without going inside the container:  

<img width="858" height="93" alt="image" src="https://github.com/user-attachments/assets/b17da711-c3d9-432e-84b1-be04c70958c4" />

If you want to delete container:  
`docker rm <cont id>` or  
`docker rm $(docker ps -a)` -> to remove all the containers  

### Creating Docker Image

#### 1. Commit method  

- Will create container from exisiting server.
- Download ngnix and create a html inside that container
- Create image out of that container
- Will create a container then out of that image
- Now will see if able to access the website or not

create a vm  
```
apt -get update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
docker --version
docker run -it --name ank_cont ubuntu /bin/bash
```

Now you are inside ank_cont

```
apt-get update
apt-get install nginx
apt-get install vim (choose asia and 44)
cd /var/www/html
vim index.html ---> <html><body><h1> Hello world </h1></body></html>
```

Now our container is ready , so move out of it and create image of that container using commit method:  
```
exit
docker commit <container id > ankita-image
```

<img width="1418" height="345" alt="image" src="https://github.com/user-attachments/assets/559e1230-3f7e-40ba-a76e-40437c5f39d8" />


Since image is ready named ankita-image , create container out of image and now u will see inside the container u r having same index.html  
`docker run -it -p 8080:80 --name ankita-container2 -h ank-cont2 ankita_image /bin/bash`
  
<img width="1586" height="292" alt="image" src="https://github.com/user-attachments/assets/56e3380a-29d7-4fea-af58-7d35468fd118" />

Now apply inbound rule of 8080 for webserver/apache :  

<img width="1883" height="774" alt="image" src="https://github.com/user-attachments/assets/8feea2e7-5f3e-4924-a569-390a2d57f7d8" />

Start ngnix service:  
`service start nginx` 
IP: http://<public ip of the exisiting server>:8080/  

<img width="1027" height="147" alt="image" src="https://github.com/user-attachments/assets/a129286a-0ba5-4235-b515-02eb788f4b2b" />

if U want to push the image then u should create image like this:
`docker commit <container id > docker-hub_username\ankita-image`  

```
docker commit <container id > ankitakumari346/ankita-image
docker login -u ankitakumari346
docker push ankitakumari346/ankita-image
```

Or u can use tag for exisitng image:  
```
docker tag ankita_image ankitakumari346/ankita_image
docker push ankitakumari346/ankita_image
```

<img width="1126" height="188" alt="image" src="https://github.com/user-attachments/assets/23a36e75-3d8b-4507-8bf8-b0dc5bb19c09" />

<img width="1897" height="965" alt="image" src="https://github.com/user-attachments/assets/1c2c3e68-f548-41db-a906-9c0dbcde6c2a" />

code to delete docker images:  
`docker rmi <image name>`  


#### 2. Build Method

- Create docker instance
- Install docker , apt-get update
- cd
- mkdir images
- cd images
- vi Dockerfile
```
FROM ubuntu:20.04
RUN apt-get update
RUM apt-get install -y nginx
COPY index.html /var/www/html/
ENTRYPOINT service nginx start && bash
```
- vi index.html
```
<html>
  <body>
    <h1> Hello world </h1>
  </body>
</html>
```

Now build the image (without creating container , with the help of Dockerfile)  
```
docker build -t "ankitakumari346/new_second_img" .
```
This will take all the content present inside present directory i.e Dockerfile and index.html  

<img width="1872" height="373" alt="image" src="https://github.com/user-attachments/assets/fb1d6f85-c7a7-4dd6-a7e5-5021c065d214" />

Create a container out of that image:  
```
docker run -it -p 8080:80 --name ngnix-cont ankitakumari346/new_second_img /bin/bash
```

<img width="1876" height="304" alt="image" src="https://github.com/user-attachments/assets/2465ab4c-43bb-4d6b-aeed-c60a2cd2462e" />

Now this dockerfile has already started ngnix also so we can see the website if inbound rule of 8080 implemented properly on security groups:  

<img width="764" height="204" alt="image" src="https://github.com/user-attachments/assets/2f14e393-142b-4016-b0dd-12b66c88dae8" />


Now u can push this image too:  
```
docker login -u ankitakumari346
docker push ankitakumari346/new_second_img
```


----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## CREATING AN IMAGE FROM GITHUB REPO - GAMUTKART

1. Create vm
2. take connection with the vm
3. `apt-get update -y`
4. `cd`
5. `mkdir source`
6. `cd source`
7. `git clone https://github.com/prasad-gamut/gamutkart2.git`
8. `cd gamutkart2/`
9. `ls`
    <img width="1724" height="212" alt="image" src="https://github.com/user-attachments/assets/7635dc2d-bbfb-42ca-9e23-b7e78a59b3ec" />
  
11. `cat Dockerfile`
    <img width="1096" height="261" alt="image" src="https://github.com/user-attachments/assets/66455925-63eb-48e2-b25e-acdb868cde40" />

13. Install docker on the server to create image out of this repository:
    - `curl -fsSL https://get.docker.com -o get-docker.sh`
    - `sudo sh get-docker.sh`
    
15. Now build image frm this github repo: `docker build -t "ankitakumari346/gamutkart-img" .`
    <img width="1920" height="418" alt="image" src="https://github.com/user-attachments/assets/36e1ab42-7936-4245-b63a-bc09aac0c387" />

17. Now we can see  , image is formed: `docker images`
    <img width="1198" height="115" alt="image" src="https://github.com/user-attachments/assets/695bca45-5bb7-4b49-af40-a965608d7233" />

19. Lets create container from this image and run the container: `docker run -it -p 8080:8080 --name gamut-container ankitakumari346/gamutkart-img /bin/bash`
    <img width="1875" height="247" alt="image" src="https://github.com/user-attachments/assets/6f01df87-5f8a-4d64-88ec-a5e52be3e13a" />

21. Add inbound rule of 8080 from 0.0.0.0/0
    <img width="1859" height="770" alt="image" src="https://github.com/user-attachments/assets/1eec49c2-d7c6-45b9-9e8e-d0fe4604a8e5" />

23. Go to url : http<public-ip of the server>:8080/gamutkart/
    <img width="1919" height="670" alt="image" src="https://github.com/user-attachments/assets/69deb5a8-b26b-4a74-87f9-220557131ba9" />

> [!NOTE]
> Now you can see , inside the container , due to the docker file. It automatically ran , apt-get update , installed jdk , copied the apache-tomcat.tar.gz from exisiting repo to the container/image  inside root/ , copied the .war file from target/.war from repo to the /root/apache/webapps inside container/image and when container started , it ran the startup.sh automatically.

<img width="1873" height="373" alt="image" src="https://github.com/user-attachments/assets/2d560597-3efb-4921-8450-4274e1272f28" />


25. On the server: exit from container
26. Push the image to docker-hub now:
```
docker -u login ankitakumari346
docker push ankitakumari346/gamutkart-img
```

<img width="1340" height="279" alt="image" src="https://github.com/user-attachments/assets/55b89afa-c874-4ef7-8616-7765062d462b" />

Go to docker-hub and check:  

<img width="1900" height="630" alt="image" src="https://github.com/user-attachments/assets/29f40330-388a-451c-a503-01cc9a848e1b" />

