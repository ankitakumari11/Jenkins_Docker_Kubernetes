# CI-CD PIPELINES

> [!IMPORTANT]
> - As we have seen in the previous readme file , how we automated the build and copy process using jenkins.
> - Now I can also create a pipeline , instead of manually wiritng scripts (like maven and all)

**TYPES OF PIPELINE**
- Manual pipeline
- Automatic pipeline

## Manual Pipeline
- In a single free style project there r 3 stages:
  - cloning (20 mins)
  - build (1 hr)
  - deployment (50 min)
- Now here if something goes wrong suppose at build stage , then it will again start from beginning i.e cloning.
- So to deal with this , we can create 3 free style project for each step :
  - cloning job
  - build job
  - deployment job
- here we will connect all 3 jobs and this is called manual pipeline.
- Create "Ankita-clone" job , and just check SCM ->git ->url ->save
  - <img width="1887" height="1020" alt="image" src="https://github.com/user-attachments/assets/0ddc1c6f-a685-458d-b1f2-3663ef3cc8a1" />

- Create "Ankita-build" job.
  - Dont select SCM
  - go directly to build steps
  - select execute shell
  - Now first build the ankita-clone and see the console output and check the path for the project cloned:
    - <img width="1881" height="595" alt="image" src="https://github.com/user-attachments/assets/a302a05b-681d-4c0c-be96-ccf765489e7f" />
  - Go back to "Ankita-build" job and in execute shell :
    - here cd <path> and mvn install write
    - `cd /root/.jenkins/workspace/Ankita-clone`
    - `mvn install`
    - <img width="1891" height="786" alt="image" src="https://github.com/user-attachments/assets/2043b115-18f5-4853-8526-4d2cb1a843d2" />

  - Create "Ankita-deployment" job
    - Go directly to execute shell
    - write : `scp target/srtech.war root@172.31.91.192:/distro/apache-tomcat-10.1.43/webapps/`
    - <img width="1863" height="587" alt="image" src="https://github.com/user-attachments/assets/34415410-d80a-4e99-842a-bd5382850751" />


  - Now in the **Post-build actions** in ankita-clone job , write ankita-build and then in ankita-build , write ankita-deployment
    - <img width="1312" height="376" alt="image" src="https://github.com/user-attachments/assets/5806b861-78c0-4fc7-b35d-306b20f2370d" />
    - <img width="1903" height="892" alt="image" src="https://github.com/user-attachments/assets/e2acdfaa-bfef-4913-bb7a-84e47222a567" />

  - Now click on build now for "ankita-clone"
  - Then it will build the other 2 also
    - <img width="774" height="393" alt="image" src="https://github.com/user-attachments/assets/52fa7898-a44f-40e8-b20a-172868976063" />

  sdd

> [!NOTE]
> - now when u build the "ankita-clone" , it cloned the project inside "ankita-clone" inside .jenkins/wepspaces.
> - So in the job " ankita-build" , u redirected to first ankita-clone path (cd /root/.jenkins/workspace/Ankita-clone) becoz the pom.xml is present only there.
> - Now inside Ankita-clone , a new folder created due to 2nd job i.e target/srtech.war
> - Now in the 3rd job "Ankita-Deployment" , if directly write something like this:
>  - scp target/srtech.war root@172.31.31.57/root/distros/apache-tomcat-10.1.42/webapps/
>  - It will search for target folder inside "Ankita-Deployment" folder inside .jenkins/webspaces but target is present inside Ankita-clone so that's y we gave full path
>  - `scp /root/.jenkins/workspace/Ankita-clone/target/srtech.war root@172.31.91.192:/root/distro/apache-tomcat-10.1.43/webapps`
> - Whenever a new job is run , it's directory is created.
  
  <img width="1018" height="76" alt="image" src="https://github.com/user-attachments/assets/26186806-6207-4309-9f72-6badbc98d34c" />

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## AUTOMATIC (CI/CD PIPELINE)

Create a job (pipeline job)

<img width="1855" height="637" alt="image" src="https://github.com/user-attachments/assets/554ab1e9-8f72-44c4-9166-2292dbec21d7" />

- Either write the pipeline script
- Or as a devops engineer u can also write the pipeline script in jenkins file and push in the main code repo and then pull frm there only using
- Create job-> configure -> pipeline ->definition -> pipeline script from scm - > scm -> git > url -> credentials -> branch -> script path -> jenkins file -> apply
- Github url: https://github.com/ankitakumari11/airtel-kotak.git
- jenkins file content:
- ```
  pipeline {
    agent any

    stages {
        stage('Clone-Repo') {
	    	steps {
	        	checkout scm
	    	}
        }
	stage('Build') {
		steps {
			sh 'mvn install'
		}
	}	
 
	stage ('Compile'){
	        steps {
			sh 'mvn clean compile'
                }
	}

	stage('Run Tests') {
	    steps {
	       sh 'mvn test'
	    }
	}

        stage('Package as WAR') {
            steps {
                sh 'mvn package'
            }
        }
	stage('Deployment') {
	   steps {
		sh 'scp target/srtech.war root@172.31.22.116:/root/distros/apache-tomcat-10.1.40/webapps'
	}
    }
}
}
```

  
