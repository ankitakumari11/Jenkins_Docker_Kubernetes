# JENKINS

<img width="1373" height="791" alt="image" src="https://github.com/user-attachments/assets/8cbb139c-b966-4b3b-adb1-4d01b55676ab" />


### <ins>MANUALLY BUILDING AND TESTING THE CODE WITHOUT THE USE OF JENKINS</ins>  

Lets create devops server (to pull and build the code)  

#### ->Devops server: 

Create a ec2 instance on aws:  ubuntu , t2-medium  
  
<img width="1898" height="1077" alt="image" src="https://github.com/user-attachments/assets/d02b3d89-db02-4285-ab44-4d2a2263f461" />

Login to server and run:
```
apt-get update
mkdir code
cd code
git clone https://github.com/prasad-srtech/srtech.git
rm  file1 file2 file3 file4 file5 file6 file7 file8
```
<img width="1168" height="350" alt="image" src="https://github.com/user-attachments/assets/606222e1-a560-4501-a038-619a3c5135d9" />

  
<img width="1344" height="248" alt="image" src="https://github.com/user-attachments/assets/bb07f029-ce08-4bff-b08b-369cda84b41c" />

```
apt-get install maven
mvn --version
mvn install  # this will build the code present inside srtech with the help of pom.xml
```
<img width="1852" height="446" alt="image" src="https://github.com/user-attachments/assets/f7a7069a-969d-4a9a-8fd5-25705c721566" />

    
<img width="1864" height="425" alt="image" src="https://github.com/user-attachments/assets/a7f553e2-f7b6-4c87-a72d-f1686deda104" />

```
ls # u will see target folder
cd target
ls #u will see .war file ( this is build)
```

<img width="1828" height="172" alt="image" src="https://github.com/user-attachments/assets/4fd7fef7-4598-4c92-b6ac-5f2909beebc9" />

Now the work of 1st server (source server) is done i.e building of code. Now we will copy the build code to testing server and there we will test it.

#### ->Testing Server:

- Create a server
  - Ubuntu
  - t2-medium
- launch the instance
- `sudo su -`
- `apt-get update`
- `apt-get install openjdk-17-jdk -y`  -> we are installing it becoz Apache Tomcat is written in Java
- `java --version`
- `mdir distro`
- `chmod 777 distro`
- `cd distro`

<img width="1157" height="238" alt="image" src="https://github.com/user-attachments/assets/755eb323-13e6-470e-a3b2-2300a73a0ea6" />

<br>

- Install apache tomcat 10:
  - https://tomcat.apache.org/download-10.cgi
  - copy the link of tar.gz file inside core section
    - wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.43/bin/apache-tomcat-10.1.43.tar.gz
  - tar -xzvf apache-tomcat-10.1.43.tar.gz

  
<img width="1035" height="154" alt="image" src="https://github.com/user-attachments/assets/8dc480c6-75b6-44dd-b7d0-c98fdea17af7" />

- rm apache-tomcat-10.1.43.tar.gz
Now here inside the webapps of apache tomcat , we will copy the .war file from source server. Becoz **WEBAPP** -> is the default deployment file for code for tomcat.

<img width="1799" height="255" alt="image" src="https://github.com/user-attachments/assets/3d0c934a-53ab-4924-b8ee-521e285e929b" />

Go back to source server.  

#### ->SOURCE SERVER:

Now we need to establish connection btw source and testing server. Here already ssh rule from 0.0.0.0/0 inbound is applied so no need to touch security groups. But need to enable authentication so copy the public key from source to destination server.  
  
```
cd
ssh-keygen
cd .ssh
cat key.pub
```
  
<img width="1844" height="440" alt="image" src="https://github.com/user-attachments/assets/6ad4ad75-87ba-4c00-bd3e-39d2df8cdea8" />


Now go to target server and  copy the public key from source server to destination(testing server)' s authorized keys.

<img width="1885" height="619" alt="image" src="https://github.com/user-attachments/assets/015a3e10-09fe-4f5b-8786-bee48bebf1f0" />

Now u can do ssh from source to target using private key of target server:  

<img width="1157" height="723" alt="image" src="https://github.com/user-attachments/assets/c8f16cbb-6616-43f9-b56f-2de6f7dfc60a" />

Now again go to source server and copy the .war file to target server's deployment file i.e webapps:  
```
scp target/srtech.war root@172.31.84.77://root/distro/apache-tomcat-10.1.43/webapps/
```

source:  
<img width="1858" height="207" alt="image" src="https://github.com/user-attachments/assets/8e627a1d-a964-499b-acb2-d4168257f1c9" />

target:  
  
<img width="989" height="336" alt="image" src="https://github.com/user-attachments/assets/5005a383-87fd-427b-88e2-f750f3706f57" />

#### ->Testing (target server):

Now we need to run tomcat server to test the application:
```
cd distro/tomcat-apache10/bin
./startup.sh

```

<img width="1801" height="420" alt="image" src="https://github.com/user-attachments/assets/3b783682-89e2-4f63-a6c0-3178ff9fce35" />  

Now since tomcat server's default port is 8080 , so go to security groups and add inbound tcp 8080 from 0.0.0.0/0

<img width="1879" height="852" alt="image" src="https://github.com/user-attachments/assets/35ca7bb7-e637-40e3-9dca-46e5798bc36e" />

Now go to url : http://<public ip of target server>:8080/srtech

<img width="1920" height="677" alt="image" src="https://github.com/user-attachments/assets/74303fbf-9edf-43d5-acb1-d5be3f4e4b4a" />

  
> [!IMPORTANT]
> Now rather than manually pulling code from github , building the code and copy and pasting it to testing server
> We can automate with the help of Jenkins

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### JENKINS SERVER 

- Create a server named - Jenkins-server or anything
- ubuntu  , t2-medium
- launch the server and take connection
```
apt-get update
apt-get install openjdk-17-jdk -y
java --version
mkdir distro
chmod 777 distro
cd distro
# install apache-tomcat to run host jenkins on the server (jenkins website)
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.43/bin/apache-tomcat-10.1.43.tar.gz
tar -xzvf apache-tomcat-10.1.43.tar.gz
rm apache-tomcat-10.1.43.tar.gz
```

Now u need to install jenkins:

- Go to website and download the .war file of jenkins sinde webapps of tomcat : https://www.jenkins.io/download/
- Or directly run below commands (website also has the same commands)
```
cd apache-tomcat-10.1.43/webapps
wget https://get.jenkins.io/war-stable/2.504.3/jenkins.war
```
<img width="917" height="51" alt="image" src="https://github.com/user-attachments/assets/8b9c3b4f-e4fc-4496-a6c7-d3f22b86d6f5" />
  

- Apply inbound rule of 8080 on jenkins server for tomcat
- start tomcat server now
```
cd distro/tomcat-apache10/bin
./startup.sh
```

<img width="944" height="472" alt="image" src="https://github.com/user-attachments/assets/fe78dbb5-792e-49af-b53e-82672aba822a" />

*Here .jenkins is the home directory for jenkins and since tomcat has jenkins application side it so we will write /jenkins in the url.*

- go to url : http://<public ip of server>:8080/jenkins
- From jenkins server copy the password from : cat /var/lib/jenkins/secrets/initialAdminPassword
  
<img width="1639" height="1063" alt="image" src="https://github.com/user-attachments/assets/defc89f8-3872-44bd-add0-903df9596ee4" />
  
- Install necessary plugins
  
<img width="1917" height="1063" alt="image" src="https://github.com/user-attachments/assets/8f2b7b94-b848-4684-8b1d-dc032de9325f" />

-

<img width="1817" height="1069" alt="image" src="https://github.com/user-attachments/assets/3e15a421-a8c0-41fe-ad3e-0f6688356e85" />

- Create your account and keep the credentials with yourself

<img width="1499" height="969" alt="image" src="https://github.com/user-attachments/assets/1d19117e-becd-4dcd-ad91-6a73e01906af" />

> [!NOTE]
> Now initially we created 2 servers (source and testing) , source on which devops engineer clone the code and build it and then copy the .war file to testing server\
for testing purpose.
> But now we directly create jenkins server and njenkins will pull the code , build it and copy the code to the testing server.  

Now on jenkins dashboard;
- Create a new job
- select freestyle project
   
<img width="1447" height="853" alt="image" src="https://github.com/user-attachments/assets/03fc5097-9bc8-470e-82fb-6234dc43c049" />

- Go to souce code management
  - write your github url like: https://github.com/prasad-srtech/srtech.git
  - Enter your credentials
    <img width="1481" height="937" alt="image" src="https://github.com/user-attachments/assets/c91895d4-80a0-40f5-ba0c-2b7b436fd7de" />
  - Select the branch
- Now save
- build it
  - <img width="1009" height="1043" alt="image" src="https://github.com/user-attachments/assets/05159f46-e991-4f43-9f28-5bd80dfc8043" />

- It will just pull the code from github
- In the console output , u can see where it pulled the code from github
  - <img width="1386" height="666" alt="image" src="https://github.com/user-attachments/assets/d841c5d7-2da9-410e-b4c1-be63678618be" />
- Go to the server and check if the code present
  - <img width="818" height="118" alt="image" src="https://github.com/user-attachments/assets/ec531024-5d05-4204-9cc2-fb23d640ccaf" />
  - <img width="909" height="157" alt="image" src="https://github.com/user-attachments/assets/8c83ddab-58df-4deb-b791-fe95eac84770" />


Now install maven on the jenkins server to build the code:

- `apt-get install maven`

Now go the jenkin's console 

- click on configure on the job
- In build option , select maven
- <img width="1877" height="993" alt="image" src="https://github.com/user-attachments/assets/2b7a6a33-e390-49d8-a586-9ade53bfbd25" />
- Write `install` in goal becoz since it is already maven so no need to write mvn
- <img width="1920" height="1023" alt="image" src="https://github.com/user-attachments/assets/14b3bcc1-0faf-48da-a85c-069d1b851fad" />
- Now build again and see the console output
- <img width="1884" height="1007" alt="image" src="https://github.com/user-attachments/assets/959a3e70-b288-4d77-a3ba-ee24b4f9af23" />
- Now inside `cd /root/.jenkins/workspace/srtech` , you will see target and inside target .war file is present.
- <img width="1837" height="328" alt="image" src="https://github.com/user-attachments/assets/74aa7b74-f710-4085-b8e3-d640a4da30fd" />
- Build done

> [!IMPORTANT]
> - here see initially we were creating a server , installing java , maven on it and then pulling the code and then going inside the code -> in the directory inside which pom.xml present.  
> - Then we ran maven install and it took that pom.xml file and build the code and created a directory "target" and inside which we had .war file.  
> - But now we created jenkins server , installed java , maven , jenkins , tomcat on it.  
> - Created a job and added Source code management git info and maven code i.e "install" and build it.  
> - Now jenkins pulled the code from github to its default/home directory i.e .jenkins and inside webspaces (./jenkins/webspaces) ->srtech.  
> - Then jenkins build the code and inside ./jenkins/webspaces/srtech (since pm.xml present) so created a target file and build .war file.  



### TESTING SERVER 
- Create a testing server on which testing will be done.
- apt-get update
- apt-get install openjdk-17-jdk -y
- mkdir distro
- chmod 777 distro
- cd distro
- wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.43/bin/apache-tomcat-10.1.43.tar.gz
- ls
- tar -xzvf apache-tomcat-10.1.43.tar.gz
- rm apache-tomcat-10.1.43.tar.gz
- cd tomcat/webapps
*Now here we are going to copy the .war file present inside jenkins server and then testing team will test the code*

Now go to jenkins console

- Go to the job
- Configure
- In source code management - select None , as we have already pulled the code
- <img width="1815" height="811" alt="image" src="https://github.com/user-attachments/assets/5f4b81b5-2aa3-44f7-a11a-afd79f389762" />

- Go to build options
- Remove maven option ( as we have already build it)
- add execule shell
- `scp target/srtech.war root@172.31.91.192:/root/distro/apache-tomcat-10.1.43/webapps`
- The above ip should be private ip of testing server
- <img width="1894" height="1023" alt="image" src="https://github.com/user-attachments/assets/cdc140db-0ac8-4db7-b20a-4005dcd12adf" />

- Also on other hand u need to create a public a key on jenkins server and copy the key to testing server for authentication
- Also try to ssh atleast once : `ssh 172.31.91.192` from jenkins server to testing server
- <img width="1917" height="469" alt="image" src="https://github.com/user-attachments/assets/3abf205f-41c3-4853-b722-519d68b05bb1" />

- Come back to job configue
- now save
- build it
- <img width="1840" height="768" alt="image" src="https://github.com/user-attachments/assets/44df9535-8345-453b-a5f4-4ecd8f148e4b" />

  








