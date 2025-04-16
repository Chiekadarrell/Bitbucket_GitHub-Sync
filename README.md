# Bitbucket to GitHub Repository Synchronization

![WhatsApp Image 2025-04-16 at 04 35 29_b29e7417](https://github.com/user-attachments/assets/c987d035-ae0c-4a5b-b7fd-186e591556af)


### OBJECTIVE: Migrating your existing bitbucket repo to GitHub repo, synchronizing them in such a way that when ever a change is made in the source repository (Bitbucket) the same change will be replicated in GitHub without any manual intervention.

#1. On GitHub, create a new repository
  - On your Bitbucket, create a new repository. 
      Navigate to your Bitbucket repository and Create an access token under Repository settings > Security > Access   tokens.
      Create Repository Access Token with selecting all the "READ" Permissions.    
      Make sure to save it somewhere because you can view it just once.
       Copy the last Access token Example.
    
     { "https://x-token-auth:ATCTT3xFfGN0TSG5xC5PMZex1aEWC2wMY7j1SiYTwLpR7WTpHQ4DJ1oRfevWbd-        LVn9bRzmr3csDN4DEjT57KYlsxWcKXnk5zW17DLJ9ssRcOFFwegxzPTMS-MAfumre3yDmXup-z1nHb8XSRGI9N_McR6FRyHArzIoPIWiJSk6cQfYqfAkIw_w=65FC4ty63@bitbucket.org/demo-migration12/solstice_demo.git" }
    
    ![image](https://github.com/user-attachments/assets/ea78ac0d-ff84-4721-b7fc-053baf90474c)

    
      Navigate To Github and Import the Repository while keeping the same name
    
  - On Bitbucket, Enable Pipelines under Repository settings > Pipelines > Settings
    
   ![image](https://github.com/user-attachments/assets/f27f96e9-71c6-4b77-90b5-5ef61afad6b9)

    
  - On Bitbucket, Generate keys under Repository settings > Pipelines > SSH keys. Copy the public key to clipboard
  - On the same page, under Known hosts enter github.com as the Host address and then click Fetch followed by Add host
  - On GitHub, add the public key under repository settings > Security > Deploy keys > Add deploy key. Tick the checkbox to Allow write access
    
    ![Screen Shot 2024-01-04 at 00 35 39](https://github.com/asaphdanchi/Mirror-and-synchronizing/assets/112729006/2545afe7-52c3-4934-a181-6a1a9b06e447)
    
  - On Bitbucket, add the public key under Repository settings > Security > Access keys > Add key
    
    ![image](https://github.com/user-attachments/assets/c143e5a5-2ed8-40f9-abd4-fe7fffa9455f)

  - On Bitbucket, Create an access tokens under Repository settings > Security > Access tokens. Create Repository Access Token with  selecting all the "READ" 
    Permissions and tick the 'Read and write checkbox under Webhooks

    ![image](https://github.com/user-attachments/assets/1f251d7c-13c8-47f2-ba2d-99b99203bd34)

  - Copy the first Token Example { "ATCTT3xFfGN0-Ndb4TYH3QnfpETX- ZlTeINE95Rp05GA3mbBTgyzJ0wzlExMu7zV_unwP6lrDlcX72_vEXtd7OLwmCpzMjgG2_XkPo6bBXjBw_Na9IMyc4j6xEBcLa7u9nd9DHoxI9SZMDVXRoV6WqE3J-dUBTM1WrKjctoQ0WafCzgc1RJkg9Q=0F9CB33A" }
    
   ![image](https://github.com/user-attachments/assets/9b92c3d8-cedb-4b98-9e80-2ded6d3d9906)


  - On Bitbucket, Create a Repository variable under Repository Settings > Pipeline > Repository variable. You can name it " BITBUCKET_VARIABLE"
  - The value will be the access token you just created in the previous step  
    
    ![image](https://github.com/user-attachments/assets/e337347f-bbe3-4b10-a205-2f1ebedb76ce)

  - On Github, At the top right click on your Profile, Scroll down at the bottom click on settings
    
    ![Screen Shot 2024-01-04 at 00 45 35](https://github.com/asaphdanchi/Mirror-and-synchronizing/assets/112729006/68a20ce1-ad1e-44cb-8b84-1729dbc8b212)
    
  - At the bottom left of the page click Developer Settings, Create a Personal access tokens Under Personal access tokens > Token (classic) > Generate new token > 
    Generate new token (classic)
    
    ![image](https://github.com/user-attachments/assets/cb3d4f45-8a70-4071-a8de-f73e4c74a4d4)
    
  - Check the "repo" box, "Workflow" and the "write:package" box.
   
    ![image](https://github.com/user-attachments/assets/9402d692-d5f4-4f4c-a888-ee3d3ebf0349)

  -  Scroll down and click Genarate Token and copy the token

  - On Bitbucket, Create a Repository variables with the Personal Access Token from Github as the value. Repository Settings > Pipeline > Repository variable. You 
    can name it "GITHUB_VARIABLE".

    ![image](https://github.com/user-attachments/assets/1d593157-6f11-4fee-b420-530d43563b64)


# Inside the Bitbucket repo, create a bitbucket-pipelines.yml file

![image](https://github.com/user-attachments/assets/7e4d2ec0-7868-4dee-9458-26c53c574c39)


Paste the following:

```
  pipelines:
    default:
      - step:
          name: Sync GitHub Mirror
          image: alpine/git:latest
          clone:
            enabled: false
          script:
            - git clone --mirror https://x-token-auth:"$BITBUCKET_VARIABLE"@bitbucket.org/solavisetech_internship/bitbucket-github_sync.git ## @bitbucket.org follow by your Bitbucket repository path
            - cd bitbucket-gitHub_sync.git ## cd followed by your Github repository Name
            - git push --mirror https://x-token-auth:"$GITHUB_VARIABLE"@github.com/asaphdanchi/Mirroring-Repo.git ## @github.com followed by your Github repository path
```

On your code replace the "$BITBUCKET_VARIABLE" and "$GITHUB_VARIABLE" with your corresponding variable names while keeping the   $ and the "" sign. 

Bitbucket repository path: ![image](https://github.com/user-attachments/assets/cef151e9-b43e-43c0-932d-b433d4862f8b)

GitHub repository path: ![image](https://github.com/user-attachments/assets/d77133d0-6788-4387-a530-4d3411ccb34c)

GitHub repository name: ![image](https://github.com/user-attachments/assets/6da16431-d1a6-4c93-b43a-b8866f4a9f80)

![image](https://github.com/user-attachments/assets/b5dd5073-f17c-48c2-bee2-bfe626463d79)


# Run the pipeline in Bitbucket


![image](https://github.com/user-attachments/assets/43f9beec-b59e-4e30-805a-e19a64470b7c)

Now check your GitHub repository and see that the bitbucket-pipelines.yml was automatically added!
