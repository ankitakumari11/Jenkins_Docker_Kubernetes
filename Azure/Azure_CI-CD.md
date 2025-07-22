# Azure CI-CD Pipeline

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
  2. 2. Push image to docker-hub
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








