# GitHub Integration with Jenkins — Complete Notes
Jenkins demonstration 
This guide covers the complete integration process, from installing Jenkins plugins to configuring GitHub credentials, connecting a repository, creating a pipeline, setting up webhooks, testing automatic builds, and troubleshooting common errors.

### 1. What is GitHub integration with Jenkins?
GitHub integration allows Jenkins to access source code stored in GitHub and automatically execute CI/CD pipelines.

###  Example scenario:

A developer updates application code and pushes it to GitHub. Jenkins detects the change, downloads the latest code, and executes the pipeline.


### 2. Understand the architecture
```
          Developer

Writes and updates application code

        GitHub Repository

Stores source code and Jenkinsfile

        GitHub Webhook

Notifies Jenkins about a push event

            Jenkins

Checks out code and runs Jenkinsfile

          CI/CD Pipeline

Build → Test → Deploy
```

### 3. Prerequisites

Before starting, make sure you have:
- Jenkins installed and running.
- Access to the Jenkins dashboard.
- A GitHub account.
- A GitHub repository.
- Git installed on your Jenkins machine or agent.
- A Jenkinsfile inside your repository.

For this example, we use:
```
Item	                Example
GitHub repository	    my-devops-project
Jenkins job	          github-integration
Branch	              main
Pipeline file	        Jenkinsfile
Pipeline type	        Declarative Pipeline
```

### 4. Install required Jenkins plugins
Jenkins uses plugins to communicate with Git and GitHub.

Step 1: Open the Jenkins dashboard.
Step 2: Navigate to:
          Manage Jenkins → Plugins → Available plugins
Step 3: Search for and install these plugins if they are not already installed.
```
Plugin	            Purpose
Git	                Clone and check out Git repositories
GitHub	            GitHub-related integration and triggers
Pipeline	          Create and execute pipeline jobs
Credentials	        Store GitHub credentials securely
```

<img width="839" height="557" alt="image" src="https://github.com/user-attachments/assets/3a0118fe-44be-4c0b-8a91-cc99d825fb79" />

Select the required plugins and install them. Restart Jenkins if requested.

Note: Some plugins may already be installed as dependencies of other plugins. Check the Installed plugins tab before installing duplicates.

### 5. Create a GitHub repository
Step 1: Open GitHub.

Step 2: Select New repository.

Step 3: Enter the repository name:
```
        my-devops-project
```

Step 4: Select Public or Private.

Step 5: Create the repository.
Your repository URL will look like:
```
https://github.com/USERNAME/my-devops-project.git
```
Replace USERNAME with your GitHub username.

6. Create a Jenkinsfile in the repository
Create a file named exactly:
```
Jenkinsfile
```

Place it in the root directory of your project.

Example project structure:
```
my-devops-project/
│
├── Jenkinsfile
├── README.md
└── application/
```

Add this pipeline:
```
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code is available'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application'
            }
        }
    }
}
```

#### This is a learning example. It prints messages rather than performing a real application build or deployment.

### 7. Push the Jenkinsfile to GitHub
Open your terminal in the project directory.

Run these commands:

Step 1 — Initialize Git (only if the project is not already a Git repository):
```
git init
```

Step 2 — Add the GitHub repository as the remote:
```
git remote add origin https://github.com/USERNAME/my-devops-project.git
```

Step 3 — Add the Jenkinsfile:
```
git add Jenkinsfile
```

Step 4 — Commit the changes:
```
git commit -m "Add Jenkinsfile"
```

Step 5 — Set the main branch:
```
git branch -M main
```

Step 6 — Push the code to GitHub:
```
git push -u origin main
```

### 8. Configure GitHub credentials in Jenkins
Credentials are needed when Jenkins accesses a private GitHub repository or performs authenticated operations.

For private repository access, a GitHub Personal Access Token (PAT) can be used with HTTPS.

Step 1: Open GitHub.

Step 2: Navigate to:
```
Settings → Developer settings → Personal access tokens
```

Step 3: Create a fine-grained token.

Step 4: Select the required repository and grant only the permissions needed. For cloning a private repository, grant read access to repository contents.

Step 5: Generate the token and copy it securely.

Security :
 Never share or commit your token in GitHub or inside your Jenkinsfile.

Step 6: Open Jenkins:
Manage Jenkins → Credentials → System → Global credentials

Step 7: Select Add Credentials.

Use these settings:
```
Field	              Value
Kind	              Username with password
Username	          Your GitHub username
Password	          Your GitHub token
ID	                github-token
Description	GitHub  access token
```
Click Create.

<img width="1934" height="1160" alt="image" src="https://github.com/user-attachments/assets/ce886226-2e08-4a89-940f-50fb1c6068a5" />

For a public repository, credentials are generally unnecessary for cloning.

### 9. Create a Pipeline job in Jenkins
Step 1: Open Jenkins Dashboard.

Step 2: Select New Item.

Step 3: Enter:
```
github-integration
```

Step 4: Select Pipeline.

Step 5: Click OK.

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/ff1691de-f8d9-474d-a56e-028b5e631651" />


### 10. Connect the GitHub repository to Jenkins
Scroll down to the Pipeline section.

Configure the following:
```
Configuration	                  Value
Definition	                    Pipeline script from SCM
SCM	                            Git
Repository URL	                https://github.com/USERNAME/my-devops-project.git
Credentials	                    github-token if private
Branch Specifier	              */main
Script Path	                    Jenkinsfile
```

<img width="973" height="688" alt="image" src="https://github.com/user-attachments/assets/a5dd4d71-b0f0-4429-887f-078e97ab4eff" />

Click Save.

#### What does Pipeline script from SCM mean?
SCM means Source Code Management.
It tells Jenkins to retrieve the pipeline script from your GitHub repository instead of manually writing the pipeline inside Jenkins.

### 11. Run the Jenkins pipeline manually
Step 1: Open the github-integration job.

Step 2: Select Build Now.

Step 3: Open the latest build number.

Step 4: Select Console Output.
Expected output includes:
```
Source code is available
Building application
Testing application
Deploying application

Finished: SUCCESS
```
Expected result
 Jenkins successfully connected to GitHub and executed the Jenkinsfile.

### 12. Configure GitHub Webhook
Until now, we started the pipeline manually using Build Now.
A webhook allows GitHub to notify Jenkins automatically when a developer pushes new code.

<img width="2674" height="1768" alt="image" src="https://github.com/user-attachments/assets/53635f96-b7d7-4e35-895a-112abf5a8217" />

Step 1 — Find your Jenkins URL
Example:
```
http://YOUR-JENKINS-PUBLIC-IP:8080
```
For a real environment, use HTTPS with a secure reverse proxy or load balancer.

Step 2 — Configure the Jenkins job
1. Open the github-integration job.
2. Select Configure.
3. Find the Build Triggers section.
4. Enable GitHub hook trigger for GITScm polling.
5. Save the configuration.

<img width="1400" height="876" alt="image" src="https://github.com/user-attachments/assets/987a536c-f784-4c4b-b962-df5d5aae5da2" />

Step 3 — Configure the webhook in GitHub
1. Open your GitHub repository.
2. Select Settings.
3. Select Webhooks.
4. Click Add webhook.
Configure:
```
Field	                Value
Payload URL	          https://YOUR-JENKINS-DOMAIN/github-webhook/
Content type	        application/json
Secret	              Use a configured webhook secret if supported by your setup
SSL verification	    Enable for HTTPS
Events	              Just the push event
Active	              Enabled
```

For a Jenkins server without a domain, the payload URL can use its reachable public IP:
```
http://YOUR-JENKINS-PUBLIC-IP:8080/github-webhook/
```

Use HTTPS in production. The Jenkins endpoint must be reachable from GitHub. Do not expose Jenkins broadly to the internet just to make webhooks work.

Step 4 — Save the webhook
Click Add webhook.
GitHub will send a test request. Check the webhook's Recent Deliveries section for the response.

### 13. Test automatic triggering
Now test the complete integration.
Make a small change to your project, such as updating README.md.

Run:
```
git add README.md
git commit -m "Test GitHub webhook"
git push origin main
```

#### Expected workflow:
1. Developer pushes code to GitHub.
2. GitHub sends a webhook notification.
3. Jenkins receives the notification.
4. Jenkins starts a new build.
5. Jenkins retrieves the latest repository changes.
6. Jenkins executes the Jenkinsfile.
7. Jenkins displays the build result.

Successful integration
 You should see a new build appear in Jenkins without clicking Build Now.

### 14. Understand the difference between Git and GitHub plugins
```
Git plugin	                              GitHub plugin
Provides Git SCM operations	              Provides GitHub-specific integration features
Clones and checks out repositories	      Supports GitHub-related triggers and integrations
Used to retrieve source code	            Helps Jenkins respond to GitHub events
```
The Git plugin is essential for the SCM checkout in this example. The GitHub plugin supports additional GitHub integration features. Webhook setups can vary depending on the plugins and trigger configuration.



### 15. Common errors and troubleshooting
```
Error	                                    Possible                               reason	Solution
Repository not found	                    Incorrect repository URL	             Verify the GitHub clone URL
Authentication failed	                    Invalid or expired token	             Update Jenkins credentials
Jenkinsfile not found	                    Incorrect file path	                   Check Script Path and filename
Branch not found	                        Incorrect branch name	                 Verify */main
Git command not found	                    Git is not installed on the agent	     Install Git on the agent
Webhook delivery failed	                  Jenkins is unreachable	               Check URL, network, firewall and HTTPS
Webhook delivered but no build	          Trigger not configured	               Check the job's Build Triggers
Build fails	                              Pipeline syntax or command error	     Inspect Console Output
No executor available	                    No online agent or free executor	     Check Jenkins nodes and executors
```










