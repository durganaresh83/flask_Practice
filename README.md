# Jenkins CI/CD Pipeline Assignment

# Open Cloud Jenkins
https://jenkinsacademics.herovired.com/

# Install required Jenkins plugins
Manage Jenkins → Plugins → Available Plugins <br>
Install: <br>

. GitHub Integration <br>
. Git Plugin <br>
. Pipeline <br>
. Email Extension Plugin <br>

# Fork and Prepare the Application Repository
https://github.com/durganaresh83/flask_Practice.git

# Jenkins Pipeline
Create a Jenkinsfile in the root directory of your project: <br>

pipeline { <br>
    agent any <br>

    stages { <br>

        stage('Build') { <br>
            steps { <br>
                echo "Installing dependencies..." <br>
                sh 'pip3 install -r requirements.txt' <br>
            } <br>
        } <br>

        stage('Test') { <br>
            steps { <br>
                echo "Running tests..." <br>
                sh 'pytest || exit 1' <br>
            } <br>
        } <br>

        stage('Deploy') { <br>
            when { <br>
                expression { <br>
                    currentBuild.result == null || currentBuild.result == 'SUCCESS' <br>
                } <br>
            } <br>
            steps { <br>
                echo "Deploying Flask app to staging environment..." <br>
                sh ''' <br>
                    # Example simple deployment <br>
                    nohup python3 app.py & <br>
                ''' <br>
            } <br>
        } <br>
    } <br>

    post { <br>
        success { <br>
            emailext subject: "Build Successful: ${env.JOB_NAME}", <br>
                     body: "The Jenkins build was successful.", <br>
                     recipientProviders: [developers()] <br>
        } <br>
        failure { <br>
            emailext subject: "Build Failed: ${env.JOB_NAME}", <br>
                     body: "The Jenkins build has failed.", <br>
                     recipientProviders: [developers()] <br>
        } <br>
    } <br>
} <br>

# Create a Jenkins Pipeline Job -  Follow the steps below to create the Jenkins job
**Steps:**

. Open Jenkins → New Item <br>
. Choose Pipeline <br>
. Name it: durga-flask-ci-cd <br>

<img width="1592" height="887" alt="Screenshot 2025-11-19 195019" src="https://github.com/user-attachments/assets/dd05ed08-b20c-4dd7-8b0c-feb23f7a822b" />

. Under Pipeline → Definition → choose Pipeline script from SCM <br>
. SCM → Git <br>

<img width="1367" height="745" alt="image" src="https://github.com/user-attachments/assets/570f74e6-8075-4f59-8466-71314e609f17" />

# Adding the Branch condition - which branch to be triggered
<img width="1317" height="750" alt="image" src="https://github.com/user-attachments/assets/5345d28b-505c-44bf-ae18-9e0c66f53d56" />

**Click Save**

# Configure Webhook Trigger - Automatic Build on Push
In GitHub: <br>
. Go to your repo → Settings → Webhooks → Add Webhook <br>

Payload URL: https://<your-jenkins-domain>/github-webhook/ <br>
Content type: application/json <br>
Trigger: ✔ "Just the push event" <br>

<img width="1476" height="898" alt="Screenshot 2025-11-19 214520" src="https://github.com/user-attachments/assets/cb3f2bcf-305d-45e0-9a8b-156c158d0a50" />

# In Jenkins - Triggers Setup:

Open the same job → Configure → Build Triggers <br>
✔ GitHub hook trigger for GITScm polling <br>

<img width="1033" height="488" alt="image" src="https://github.com/user-attachments/assets/ebfce461-45d9-49b8-932e-6e2e9cc59190" />

# Notifications:
Go to:
. Manage Jenkins → System <br>

. Set SMTP server <br>
(Example Gmail SMTP) <br>

. Enable TLS <br>
. Add credentials for your email <br>

<img width="1172" height="796" alt="image" src="https://github.com/user-attachments/assets/5ffbb80e-464a-4740-b773-2e83fa7fab32" />

. Test email <br>

<img width="1007" height="297" alt="image" src="https://github.com/user-attachments/assets/730d9a77-70a5-4edf-8230-4495f1a9e936" />

# Screenshots <br>

**1. Checkout Stage** <br>
In this stage, clone the repository to the Jenkins node <br>

<img width="1391" height="762" alt="image" src="https://github.com/user-attachments/assets/13a7c2eb-8dd1-42f3-a544-e612ecbf199d" />



**2. Build Stage** <br>

<img width="1203" height="810" alt="image" src="https://github.com/user-attachments/assets/bfcbd518-fdbf-42a7-a249-932fb79580be" />


**From the requirements.txt, install the required software <br>**

<img width="1396" height="818" alt="image" src="https://github.com/user-attachments/assets/90eac46d-6bbc-4ab8-a243-8ea398784cd0" />



**3. Test Stage**
In the testing stage, I am pulling the MongoDB Docker image <br>

<img width="1252" height="808" alt="image" src="https://github.com/user-attachments/assets/e78967b6-1131-4f9e-96b1-eb19e3a33ed0" />

Once the image is pulled, running the unit test cases <br>


<img width="1065" height="771" alt="image" src="https://github.com/user-attachments/assets/4f681739-5d9a-494e-9821-c5f883ab2026" />

After the testing is completed, stop the MongoDB. <br>



**4. Deploy Stage** <br>

In the deploy stage, deploy the Flask application <br>

<img width="1252" height="812" alt="image" src="https://github.com/user-attachments/assets/00d22ce6-1ba1-4e25-b123-dcc1690ec482" />


After the deployment, checking the health status of the application <br>

<img width="946" height="536" alt="image" src="https://github.com/user-attachments/assets/39afb3cd-857e-4754-8dd7-ad5b1686ca00" />



**5. Notification** <br>
A notification system to alert via email when the build process fails or succeeds. <br>

<img width="822" height="427" alt="image" src="https://github.com/user-attachments/assets/6de90074-3843-480f-94c1-1c0220f2f498" />

