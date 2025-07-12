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
x
```

Now u need to install jenkins:

- Go to website : https://www.jenkins.io/doc/book/installing/linux/
- Or directly run below commands (website also has the same commands)
  
```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
- Apply inbound rule of 8080 on jenkins server for tomcat
- start tomcat server now
```
cd distro/tomcat-apache10/bin
./startup.sh
```
- go to url : http://<public ip of server>:8080/jenkins
- From jenkins server copy the password from : cat /var/lib/jenkins/secrets/initialAdminPassword
- Install necessary plugins
  
<img width="1917" height="1063" alt="image" src="https://github.com/user-attachments/assets/8f2b7b94-b848-4684-8b1d-dc032de9325f" />

-

<img width="1817" height="1069" alt="image" src="https://github.com/user-attachments/assets/3e15a421-a8c0-41fe-ad3e-0f6688356e85" />

- Create your account and keep the credentials with yourself

<img width="1499" height="969" alt="image" src="https://github.com/user-attachments/assets/1d19117e-becd-4dcd-ad91-6a73e01906af" />
