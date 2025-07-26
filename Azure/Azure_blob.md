# Azure blob

1.Navigate to "storage accounts" in azure:

   <img width="1920" height="874" alt="image" src="https://github.com/user-attachments/assets/4c6b6f13-d232-4d11-9d2f-07ec588ce0b8" />

   <img width="1906" height="948" alt="image" src="https://github.com/user-attachments/assets/e7464d06-fe7f-4ff5-9907-6611a00f3010" />

2. After deployment , go to the storage account and navigate to "containers".

   <img width="1919" height="899" alt="image" src="https://github.com/user-attachments/assets/ee0d44e5-9fa4-4f4a-9fa5-18d7e973086b" />

3. Create a new container and set the access level (private,blob or container):

   <img width="1914" height="960" alt="image" src="https://github.com/user-attachments/assets/690c94a0-8e5a-4dff-bf4d-adc001e8ac0e" />

4. Open the container and click "upload" to add files.

   <img width="1920" height="654" alt="image" src="https://github.com/user-attachments/assets/d88260d2-c300-4358-8606-cc967d985906" />

5. Select files from your local system and click "upload"

   <img width="1920" height="781" alt="image" src="https://github.com/user-attachments/assets/6d9259ba-f987-4d1f-add2-eeeb19d8d4be" />

6. Now u can see , the file is uploaded. Now go inside your file.

    <img width="1906" height="612" alt="image" src="https://github.com/user-attachments/assets/23ad61ef-7fc2-4be0-bede-be9e15b86441" />

7. Now try to access the url to see the content of the file:

    <img width="1899" height="861" alt="image" src="https://github.com/user-attachments/assets/86983fe3-da41-405f-bf13-53d7ca72dd99" />

8. Now u can see, u r not able to access it as public access is prohibited becoz u select access level of container as private.

    <img width="1920" height="271" alt="image" src="https://github.com/user-attachments/assets/35a37e30-d1fe-462c-b103-85337c94fcb8" />

9. Now you need to change the access level of container here:

    <img width="1920" height="463" alt="image" src="https://github.com/user-attachments/assets/a634c7fe-dabc-458b-a779-fbd13a50db79" />

10. Here the access should be : blob level but first we need to give access to storage:

    <img width="1914" height="563" alt="image" src="https://github.com/user-attachments/assets/d4186804-b728-46bf-8288-e509ab99731d" />

11. Go to storage account and change the access level here first:

     <img width="1920" height="962" alt="image" src="https://github.com/user-attachments/assets/0bc4c61d-68f3-48dc-a4d3-91f468835b22" />

     <img width="1919" height="968" alt="image" src="https://github.com/user-attachments/assets/6b51150e-8454-4e3f-b21b-bc13ee2974d4" />

12. Now u can change the access level of the container and now try to access the url:

    <img width="1920" height="643" alt="image" src="https://github.com/user-attachments/assets/4be61c79-a244-4ad8-9a69-ad5b61b23840" />

    <img width="1920" height="150" alt="image" src="https://github.com/user-attachments/assets/1c84e754-d7fa-447e-92c0-5b969cbbdbfd" />

## 🧱 What Happened:  
  
- Create Storage Account → Your Azure "building" for storing data.
- Create a Container → Like a folder inside your storage account.
- Upload a File (Blob) → You uploaded a file like cat.jpg.
- Try to Access the File via URL → It failed because you had set the container's access level to Private.

**Why Access Failed**

- Private means only authenticated users/services can access the file.
- No public (browser) access allowed.

**🔐 Access Levels in Azure Blob Storage (for Containers):**
| **Access Level** | **Who Can Access?**                                      |
|------------------|----------------------------------------------------------|
| Private          | Only authorized users via Azure login/keys               |
| Blob             | Anyone with the blob URL can access the file             |
| Container        | Anyone can list all blobs in the container and access them |

**🛠️ What You Did to Fix It:**

- Changed container access level to Blob.
- Allowed public access to the storage account itself.
- After both changes, the blob URL worked in a browser.



