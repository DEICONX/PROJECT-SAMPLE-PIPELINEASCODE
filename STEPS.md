

PIPELINE AS CODE-DOCUMENTATION
SERVER CREATION AND JENKINS SET UP
For this project, we need multiple servers for each tool to support distributed builds.

Create and set up the Test, Build, Repository, and Deploy servers by following the instructions in the GitHub repositories below:

https://github.com/DEICONX/PROJECT-SONAR-JAVA.git (for SonarQube setup)

https://github.com/DEICONX/PROJECT-JAVA-BUILD-DEPLOY-.git (for Maven and Tomcat setup)

https://github.com/DEICONX/PROJECT-NEXUS-JAVA.git (for Nexus setup)

https://github.com/DEICONX/PROJECT-SAMPLE-JENKINS.git (for Jenkins installation)

Now that the servers and tools are set up, SSH into your Jenkins server terminal.

Now, let's generate SSH keys. Log in as the jenkins user:

sudo su jenkins

Generate the key pair:

ssh-keygen

This will create a private key (id_rsa) and a public key (id_rsa.pub) in the /var/lib/jenkins/.ssh directory.

I am now connected to the Jenkins web browser using <public_ip>:8080.

Change the directory to .ssh:

cd .ssh

Use ls to see the private and public keys.

Read and copy the public key (cat id_rsa.pub).

Go to the SonarQube server terminal.

Run ls -a to find the .ssh folder.

cd .ssh

ls - you should find an authorized_keys file.

Edit the file: sudo nano authorized_keys

Paste the Jenkins public key into this file and save it.

Go to the Nexus server terminal.

Run ls -a to find the .ssh folder.

cd .ssh

ls - you should find an authorized_keys file.

Edit the file: sudo nano authorized_keys

Paste the Jenkins public key here and save it.

Go to the Tomcat (Deploy) server terminal.

Run ls -a to find the .ssh folder.

cd .ssh

ls - you should find an authorized_keys file.

Edit the file: sudo nano authorized_keys

Paste the Jenkins public key here and save it.

Now, the Jenkins server can SSH into the other servers using its public key.

We can confirm this by manually logging in from the Jenkins server to each other server using its IP address (e.g., ssh ubuntu@<other_server_ip>).

Open the Jenkins web browser and install the necessary plugins for our pipeline:

GitHub, Maven Integration, SonarQube Scanner, Nexus Artifact Uploader, SSH, Pipeline, Deploy to container

We are ready with Jenkins. Now, let's add credentials.

ADDING CREDENTIALS
Go to Manage Jenkins ----> Credentials.

You will see a dashboard. Select System.

Now select Global credentials (unrestricted).

Click Add Credentials. We will add our SonarQube token here.

Kind: Secret text

Secret: Your SonarQube token

ID: sonar-token (or a descriptive ID you will remember)

Click Create.

Now, we will add the Jenkins private key (which we generated with ssh-keygen).

Kind: SSH Username with private key

Username: jenkins

Private Key: Select Enter directly.

Paste your private key (copied from /var/lib/jenkins/.ssh/id_rsa).

Click Create.

We have successfully created and added our credentials to Jenkins.

![](CREDENTIALS/Screenshot%202025-11-03%2E 143903.png)

SYSTEM AND TOOLS
Go to Manage Jenkins ----> Configure System.

Scroll down to the SonarQube servers section.

Click Add SonarQube.

Name: SonarQube (or your preferred name)

Server URL: http://<your_sonarqube_ip>:9000

Server authentication token: Select the SonarQube token credential you just created.

Click Save.

On every server (Sonar, Nexus, Tomcat), create a jenkins workspace directory:

mkdir jenkins

Using pwd, we can find the path: /home/ubuntu/jenkins

We need the Java and Maven paths to configure tools in Jenkins.

Find the Java path: readlink -f $(which java)

Find the Maven path: readlink -f $(which mvn)

Note: Copy the path up to (but not including) the /bin directory.

Go to Manage Jenkins --> Global Tool Configuration --> JDK --> Add JDK.

Name: JDK-11 (or your version)

Uncheck Install automatically.

JAVA_HOME: Paste the copied Java path.

Note: Add all Java versions that you have installed on your servers.

For Maven, use readlink -f $(which mvn) and copy the path before /bin.

Configure the Maven tool in Global Tool Configuration.

Click Add Maven.

Name: Maven-3 (or your version)

Uncheck Install automatically.

MAVEN_HOME: Paste the copied Maven path.

Click Save.

NODE CREATION
Go to Manage Jenkins --> Nodes.

You will see the built-in node, which is the Jenkins controller itself.

Click on New Node to create an agent.

Our first node is for our Sonar/Maven server.

Node name: sonar-mvn

Select Permanent Agent.

Click Create.

Description: Agent for SonarQube and Maven builds

Number of executors: 1

Remote root directory: /home/ubuntu/jenkins (This is the workspace folder we created earlier).

Labels: sonar (This is very important; we use this label to select the agent in our pipeline).

Launch method: Launch agents via SSH

Host: Private IP address of the Sonar/Maven server

Credentials: Select the jenkins (SSH private key) credential.

Host Key Verification Strategy: Non-verifying Verification Strategy

Click Save.

Our sonar-mvn server node is created and connected successfully.

We can view our node in the Nodes dashboard.

Repeat this same process for the Nexus and Tomcat servers, giving them unique names (nexus, tomcat) and labels (nexus, tomcat).

Now we have created a node for each server and connected it to Jenkins.

PIPELINE AS CODE-JOB CREATION
On the Jenkins dashboard, go to New Item.

Enter a name for your job (e.g., my-java-pipeline).

Select Pipeline.

Click OK.

You will be directed to the configuration page. Scroll down to the Pipeline section.

Pipeline script

For our sonar-mvn server, the script is:

Groovy

pipeline {
    agent none
    stages {
    stage('sonar-mvn') {
        agent { label 'sonar' }
        steps {
            script {
                sh '''
                    echo "hii from sonar" >> sonar.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
}
}


**Note:** Here, we must add our label (`sonar`) in the `agent` block.
For our Nexus server, the pipeline script is:

Groovy

pipeline {
    agent none
    stages {
    stage('sonar-mvn') {
        agent { label 'sonar' }
        steps {
            script {
                sh '''
                    echo "hii from sonar" >> sonar.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
    stage('nexus') {
        agent { label 'nexus' }
        steps {
            script {
                sh '''
                    echo "hii from nexus" >> nexus.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
}
}


We can add any number of stages under the `stages` block.
For our Tomcat deploy server, the pipeline script is:

Groovy

pipeline {
    agent none
    stages {
    stage('sonar-mvn') {
        agent { label 'sonar' }
        steps {
            script {
                sh '''
                    echo "hii from sonar" >> sonar.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
    stage('nexus') {
        agent { label 'nexus' }
        steps {
            script {
                sh '''
                    echo "hii from nexus" >> nexus.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
    stage('tomcat') {
        agent { label 'tomcat' }
        steps {
            script {
                sh '''
                    echo "hii from tomcat" >> tomcat.txt
                    whoami
                    hostname -i
                '''
            }
        }
    }
}
}

Go to your Job and click on Build Now.

You can see the pipeline stages are formed and the build is successful.

The console output of our sample pipeline script.

We can view the output files (e.g., sonar.txt) in each server's workspace directory (/home/ubuntu/jenkins/workspace/<job_name>).
