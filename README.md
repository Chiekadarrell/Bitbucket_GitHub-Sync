# Bitbucket to GitHub Repository Synchronization

![WhatsApp Image 2025-04-16 at 04 35 29_b29e7417](https://github.com/user-attachments/assets/c987d035-ae0c-4a5b-b7fd-186e591556af)


### OBJECTIVE: Setting up automatic synchronization between Bitbucket and GitHub—so any code changes in Bitbucket instantly copy to GitHub.

## Setting up Bitbucket and GitHub
1. Create repo on Bitbucket

   - Reason: This serves as your primary repository where development happens. You need a source repo to sync from.
   - Process: Go to https://bitbucket.org, create an account if you do not have one and create a new repository.

   <img width="960" alt="Creating a Bitbucket GitHub" src="https://github.com/user-attachments/assets/2f0208d7-9c4c-4e7b-abdc-6f908c52ba70" />

   
2. Create repo on GitHub
   - Reason: This is your destination repository that needs to stay in sync with Bitbucket. You might need this for GitHub-specific features or audience.
   - Process: Go to https://github.com, create an account if you do not have one and create a new repository.
  
     
<img width="960" alt="Creating a GitHub Repo" src="https://github.com/user-attachments/assets/1eb0c4da-f300-4a21-a1e2-79f74b2df16d" />

3. Create access token with read and write permissions on Bitbucket
   - Reason: The pipeline needs authentication to access your Bitbucket repo. Read permissions allow fetching changes, write allows pushing updates back if needed.
   - Process: Navigate to your Bitbucket Repository settings on the left pane > Security > Access tokens.
              Select the **READ** Permissions and check the **Read and write checkbox under Webhooks** as shown below. 
              Copy the first Token and save it in your notepad because you can view it just once.
     ![image](https://github.com/user-attachments/assets/1f251d7c-13c8-47f2-ba2d-99b99203bd34)
              
     

4. Enable pipelines in Bitbucket
   - Reason: Bitbucket Pipelines provides the automation framework that will execute your sync script whenever changes occur.
   - Process: Navigate to Repository settings > Pipelines > Settings

     ![image](https://github.com/user-attachments/assets/f27f96e9-71c6-4b77-90b5-5ef61afad6b9)

5. Create an SSH key
   - Reason: SSH provides secure authentication between servers. You'll need this for the pipeline to communicate with GitHub without password prompts.
   - Process: Navigate to Repository settings > Pipelines > SSH keys. Copy the public key to clipboard

6. GitHub.com as host
   - Reason: When setting up SSH, you specify GitHub.com as the host to ensure the keys are used specifically for GitHub operations.
   - Process: On the same page as SSH key in step 5 above, under Known hosts enter github.com as the Host address and then click Fetch followed by Add host.
  
7. Add SSH key in deploy keys in GitHub
   - Reason: Deploy keys grant repository-specific access. This allows your Bitbucket pipeline to push to your GitHub repo.
   - Process: Navigate to https://github.com and add the SSH key under repository settings > Security > Deploy keys > Add deploy key. Tick the checkbox to Allow write access

8. Add SSH keys access keys
   - Reason: This makes the SSH key available to your Bitbucket pipeline environment so it can authenticate with GitHub.
   - Process: Navigate to your Bitbucket Repository settings on the left pane > Security > Access keys and paste your SSH key there. Leave everything else as it is and proceed.

![image](https://github.com/user-attachments/assets/c143e5a5-2ed8-40f9-abd4-fe7fffa9455f)

9. Create a personal access token on GitHub
   - Reason: Provides an alternative authentication method (HTTPS-based) if SSH encounters issues. Offers more granular permission control.
   - Process: On https://github.com, At the top right click on your Profile, scroll down at the bottom click on settings

     <img width="960" alt="GitHub Settings" src="https://github.com/user-attachments/assets/6f9842dc-0fb6-467b-972e-9992d12ace85" />

        - At the bottom left of the page click Developer Settings, Create a Personal access tokens Under Personal access tokens > Token (classic) > Generate new token >
        - Generate new token (classic)
          ![image](https://github.com/user-attachments/assets/cb3d4f45-8a70-4071-a8de-f73e4c74a4d4)
        - Check the "repo" box, "Workflow" and the "write:package" box.

          ![image](https://github.com/user-attachments/assets/9402d692-d5f4-4f4c-a888-ee3d3ebf0349)

        - Scroll down and click Genarate Token and copy the token


10. Create repository variable and paste the Personal Access Token create in GitHub step 9 above
    
   - Reason: Storing the GitHub Personal Access Token as a secured repository variable keeps it accessible to pipelines but not exposed in your code.
   - Process:Navigate to your bitbucket Repository Settings > Pipeline > Repository variable and create a new repository value with a name such as "GITHUB_VARIABLE" or anything of your choice. This will be referenced in your bitbucket-pipelines.yml file
   - Paste the Personal Access Token from GitHub as the value.
     
11. Create another repository variable for the Bitbucket access token we created in step 3.
   - Reason: Similar to above, this securely provides the pipeline with Bitbucket access credentials.
   - Process: Navigate to your bitbucket Repository Settings > Pipeline > Repository variable and create a new repository value with a name such as "GITHUB_VARIABLE" or anything of your choice. This will be referenced in your bitbucket-pipelines.yml file.
   - Paste the Access Token you generated in step 3.

     ![image](https://github.com/user-attachments/assets/1d593157-6f11-4fee-b420-530d43563b64)



## Creating the bitbucket-pipelines.yml file


   - Click on your repository name on the left pane to go to your repository home page
   - Click on the 3 dots and click on add file as shown in the image below.

<img width="960" alt="Adding a file in bitbucket repo" src="https://github.com/user-attachments/assets/49a1ea79-8d46-4ef1-8a3f-7108c2a50c1b" />

Paste in the following:

```
 pipelines:
  default:
    - step:
        name: Bitbucket GitHub Sync
        image: alpine/git:latest
        clone:
          enabled: false
        script:
          - git clone --mirror https://x-token-auth:$BITBUCKET_VARIABLE@bitbucket.org/chiekadarrell/bitbucket-github-sync.git
          - cd bitbucket-github-sync.git
          - git push --mirror https://x-token-auth:$GITHUB_VARIABLE@github.com/Chiekadarrell/bitbucket-github-sync.git
```

### Pipelines file explanation

1. pipelines: (The Starting Point)
This tells Bitbucket: "Hey, the following instructions are for a Pipeline!"
Everything inside this block defines what the Pipeline should do.

2. default: (The Default Pipeline)
This means: "Run these steps every time a change is pushed to the repository."
If you don’t specify a branch (like main or develop), it runs on all changes.

3. - step: (A Single Task in the Pipeline)
A "step" is like a single job in the automation process.
Here, we define one step called "Sync GitHub Mirror."

4. name: Sync GitHub Mirror (Human-Readable Name)
This is just a label for the step (helps you identify it in logs).
Example: "This step syncs Bitbucket with GitHub."

5. image: alpine/git:latest (The "Worker" Environment)
Pipelines run inside containers (like tiny virtual machines).
alpine/git:latest is a lightweight Linux environment with Git installed.
Why? Because we need Git commands (clone, push) to work.

6. clone: enabled: false (Disable Default Cloning)
Normally, Bitbucket automatically clones your repo when a pipeline runs.
But since we’re doing a manual clone (next step), we disable this to avoid conflicts.

7. script: (The Actual Commands to Run)
This is where the real work happens. The script contains 3 Git commands:

Command 1: git clone --mirror (Copy Entire Bitbucket Repo)
- git clone --mirror https://x-token-auth:"$BITBUCKET_VARIABLE"@bitbucket.org/your-bitbucket-repo.git

What it does:
- git clone = Copies a repository.
- --mirror = Copies everything (branches, tags, history, settings).
- x-token-auth:"$BITBUCKET_VARIABLE" = Uses the access token (password) we stored earlier.
- @bitbucket.org/your-bitbucket-repo.git = The source repo (Bitbucket).
  
Why --mirror?
- Normal clone only copies the latest code.
- --mirror copies everything, making it an exact duplicate.
  
Command 2: cd (Move into the Cloned Folder)

- cd your-bitbucket-repo.git
What it does:

- cd = "Change directory" (like opening a folder): Moves into the cloned repo so we can work on it.
  
- Command 3: git push --mirror (Push to GitHub): git push --mirror https://x-token-auth:"$GITHUB_VARIABLE"@github.com/your-github-repo.git
  
What it does:
- git push = Sends changes to another repository.
- --mirror = Pushes everything (same as clone --mirror).
- x-token-auth:"$GITHUB_VARIABLE" = Uses the GitHub access token we stored.
- @github.com/your-github-repo.git = The destination repo (GitHub).
  

#### How This Works in Practice
- You push code to Bitbucket (e.g., update a file).
- Bitbucket Pipeline triggers automatically.
- The script runs:
      - Clones the full repo from Bitbucket.
      - Pushes the full repo to GitHub.
      - GitHub now has the exact same code as Bitbucket!
  
#### Key Takeaways
- The bitbucket-pipelines.yml file is like a set of instructions for automation.
- It uses Git commands (clone, push) to copy data.
- --mirror ensures everything is copied, not just the latest files.
- Access tokens: ($BITBUCKET_VARIABLE, $GITHUB_VARIABLE) keep the process secure.


### 12. Run the Pipeline (Start the Sync)

Why?: To test if everything works.

How?: Commit the bitbucket-pipelines.yml file.
Bitbucket will automatically run the pipeline.

![image](https://github.com/user-attachments/assets/a41eb764-025f-4b76-a914-e59e06b8dfda)


Check GitHub—your files should now be there!

![image](https://github.com/user-attachments/assets/1e02d895-422c-4d32-a812-1581d90cdbd6)


### Final Result: Automatic Sync! 🎊
Also, I created a simple text.txt file in bitbucket to see if it will be automatically replicated into my GitHub repo and it worked!
Now, any change in Bitbucket (new files, updates, deletions) will instantly copy to GitHub. No manual work needed!

Summary:
- Bitbucket = Source (original files).
- GitHub = Destination (copied files).
- Access Tokens = Special passwords for secure access.
- Pipeline = Robot that copies files automatically.
- SSH Keys = Secure tunnel for safe transfer.
