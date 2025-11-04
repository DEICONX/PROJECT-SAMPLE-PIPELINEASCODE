# PIPELINE AS CODE-DOCUMENTATION

##### SERVER CREATION AND JENKINS SET UP

----

Here we need multiple servers for each individual tool we call it distributed builds

> Create and setup Test, Build, Repository, Deploy servers by following the below github URL's 
>
> https://github.com/DEICONX/PROJECT-SONAR-JAVA.git  for sonarqube setup
>
> https://github.com/DEICONX/PROJECT-JAVA-BUILD-DEPLOY-.git  for maven setup and tomcat setup
>
> https://github.com/DEICONX/PROJECT-NEXUS-JAVA.git   for nexus setup
>
> https://github.com/DEICONX/PROJECT-SAMPLE-JENKINS.git  for Jenkins installation
>
> Now the servers and setup of each tool is ready SSH into terminal 

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 125214.png)

--------------------------

> Now lets generate ssh keys by log in as jenkins user 
>
> `sudo su jenkins` and now generate key using 
>
> `ssh-keygen` we will now have a private key and public key stored in .ssh 

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142323.png)

--------------------------------------

> Now iam connected to web browser of jenkins using `<public ip>:8080`

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142332.png)

---------------------------------

> change directory to .ssh `cd .ssh` 
>
> ls and you can see/read the private and public keys
>
> copy the public key

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142401.png)

-----------------------------------

> go to sonar server terminal`ls -a` you will find a .ssh folder `cd .ssh` 
>
> `ls` you find authorized_keys and `sudo nano authorized keys` 

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142459.png)

-----------------------------------------

> Paste the public key here and save it

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142527.png)

------------------------------------------------------

> go to nexus server terminal `ls -a` you will find a .ssh folder `cd .ssh` 
>
> `ls` you find authorized_keys and `sudo nano authorized keys` 

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142601.png)

------------------------------

> Paste the public key here and save it

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142714.png)

-------------------------

> go to nexus server terminal `ls -a` you will find a .ssh folder `cd .ssh` 
>
> `ls` you find authorized_keys and `sudo nano authorized keys` 

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142746.png)

-----------------------------------

> Paste the public key here and save it

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142813.png)

---------------------------

> Now jenkins can itself ssh into other servers by using ssh public key
>
> we can confirm that by log in manually using each server public ip

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 142948.png)

-----------------------

> Open jenkins web browser and install necessary plugins for our pipeline 
>
> Github,maven,sonarqube,artifact,ssh,pipeline,tomcat,nexus
>
> We are ready with jenkins and now lets add credentials

![](C:\Users\jagan\Downloads\PIPELINE DOC\JENKINSCONF\Screenshot 2025-11-03 143356.png)

--------------------------------------

### ADDING CREDENTIALS

------------

> Go to settings---->credentials
>
> you will see a dashboard select system 

![](C:\Users\jagan\Downloads\PIPELINE DOC\CREDENTIALS\Screenshot 2025-11-03 143538.png)

---------------------------------------

> Now select Global Credentials

![](C:\Users\jagan\Downloads\PIPELINE DOC\CREDENTIALS\Screenshot 2025-11-03 143548.png)

-------------------

> Add credentials and now we will add our sonarqube token here, create
>
> kind--->secret text, ID----->your token

![](C:\Users\jagan\Downloads\PIPELINE DOC\CREDENTIALS\Screenshot 2025-11-03 143639.png)

--------------------------------------

> Now we will add our jenkins private key which we have generated using ssh-keygen
>
> kind---->SSH username with private key
>
> private key--->Enter directly(your private key copied from jenkins .ssh) and create

![](C:\Users\jagan\Downloads\PIPELINE DOC\CREDENTIALS\Screenshot 2025-11-03 143713.png)

----------------------------------

> We have created/added our credentials to Jenkins

![](C:\Users\jagan\Downloads\PIPELINE DOC\CREDENTIALS\Screenshot 2025-11-03 143903.png)

-----------------

### SYSTEM AND TOOLS

-------------------

> Settings--->system ,scroll down
>
> you will find sonar qube installations 
>
> Name-->your name, Server URL-->your sonarqube URL, server authentication token-->your Sonar token and save 

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144025.png)

-------------------------------

> Create a folder name jenkins in every server using 
>
> `mkdir jenkins`  pwd we can find the path /home/ubuntu/jenkins

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144124.png)

----------------------

> We need Java,maven paths to configure tools in jenkins browser
>
> Use `readlink -f $(which java) `
>
> for maven use `readlink -f $(which mvn)` 
>
> Note: copy the path upto before /bin

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144332.png)

-------------------

> Settings-->tools-->JDK-->Add JDK
>
> Name-->any name, JAVA_HOME-->paste the copied path
>
> Note: Add all the java versions which you have installed in servers

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144401.png)

-----------------------

> for maven use `readlink -f $(which mvn)` and copy upto before /bin

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144622.png)

-------------------

> Configure maven tool 
>
> name-->any name, MAVEN_HOME-->paste the copied path

![](C:\Users\jagan\Downloads\PIPELINE DOC\TOOLS\Screenshot 2025-11-03 144645.png)

---------------------------

# NODE CREATION

> Settings-->nodes
>
> You will now see a built in node its is created by jenkins (default)
>
> Click on New node to create a node

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 144749.png)

-----------------------

> First node is for our sonar-mvn server 
>
> Node name --> sonarmvn
>
> type-->permanent agent-->create

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 144804.png)

-----------------------------

> Description-->about node
>
> number of executors-->1
>
> here we have to give our path that is the folder which we have created in each server Jenkins
>
> it indicates/tells jenkins where the work should be stored 
>
> labels are very important we should include them in pipeline as code to do job give any label name 

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 144859.png)

------------------------

> Here in our credentials we have given our Jenkins private key i.e ssh
>
> Launch method --> launch agents via ssh 
>
> Host-->Ip address of server
>
> credentials-->any name
>
> verification strategy-->Non verifying Verification strategy
>
> Save

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 144939.png)

---------------

> Our sonar-mvn server node is created successfully and its Connected successfully

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 145007.png)

-------------------

> we can view our node in Nodes dashboard of jenkins webpage
>
> Repeat the same process for each server and you can view Nodes image folder for reference in github

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 145017.png)

------------------

> Now we have created node for each server and connected it to jenkins

![](C:\Users\jagan\Downloads\PIPELINE DOC\NODES\Screenshot 2025-11-03 145428.png)

------------------------------

# PIPELINE AS CODE-JOB CREATION

> Jenkins dashboard-->new item-->pipeline(give it a name)
>
> You will be directed to configuration page Scroll down to write pipeline as code

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 093817.png)

-----------------------

> Pipeline script
>
> For our sonar-mvn server the script is
>
> pipeline {
>    agent none
>    stages {
>
>  
>
> ​    stage('sonar-mvn') {
> ​       agent { label 'sonar' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from sonar" >> sonar.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
> Note: Here we have to add our label in agent block

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 093827.png)

--------------------

> For  our nexus server pipeline script is
>
> pipeline {
>    agent none
>    stages {
>
>  
>
> ​    stage('sonar-mvn') {
> ​       agent { label 'sonar' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from sonar" >> sonar.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
>  
>
> ​    stage('nexus') {
> ​       agent { label 'nexus' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from nexus" >> nexus.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
> Here we can add any number of stage under stages

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 094505.png)

-----------------------

> For our tomcat deploy server the pipeline as script is 
>
> pipeline {
>    agent none
>    stages {
>
>  
>
> ​    stage('sonar-mvn') {
> ​       agent { label 'sonar' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from sonar" >> sonar.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
>  
>
> ​    stage('nexus') {
> ​       agent { label 'nexus' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from nexus" >> nexus.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
>  
>
> ​    stage('tomcat') {
> ​       agent { label 'tomcat' }
> ​       steps {
> ​         script {
> ​           sh '''
> ​             echo "hii from tomcat" >> tomcat.txt
> ​             whoami
> ​             hostname -i
> ​           '''
> ​         }
> ​       }
> ​     }
>
>  
>
>   }
>  }

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 094911.png)

------------------------

> Go to your Job and click on build now
>
> Here you can see the pipeline is formed and build is success

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 095107.png)

-------------------

> The console output of our sample pipeline script

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 095326.png)

----------------------

> We can View output in each server under /home/ubuntu/jenkins which we have given while creating nodes a work space folder is created here 

![](C:\Users\jagan\Downloads\PIPELINE DOC\PIPELINE\Screenshot 2025-11-04 095543.png)

