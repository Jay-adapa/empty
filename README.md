# empty
✅ Maven Java Automation – Short Notes
Step 1: Create Build Job (MavenJava_Build)

Open Jenkins → New Item → Freestyle Project

Name: MavenJava_Build

SCM → Git URL (repo link)
Branch: */main or */master

Build Steps

Maven → clean

Maven → install

Post-build Actions

Archive artifacts: **/*

Build other projects → MavenJava_Test (only if stable)

Save.

Step 2: Create Test Job (MavenJava_Test)

New Item → MavenJava_Test

Build Environment: Delete workspace before build

Build Step → Copy artifacts

From: MavenJava_Build

Artifacts: **/*

Build Step → Maven → Goal: test

Post-build → Archive artifacts: **/*

Save.

Step 3: Create Pipeline View

Dashboard → + → Build Pipeline View

Name: MavenJava_Pipeline

Initial job: MavenJava_Build

Save.

Step 4: Run

Click Run → check console

Build should turn Green

MavenJava_Test should trigger automatically.

✅ Maven Web Automation – Short Notes
Step 1: Create Build Job (MavenWeb_Build)

SCM → Git URL

Build Steps

Maven clean

Maven install

Post-build

Archive **/*

Build other project → MavenWeb_Test

Save.

Step 2: Create Test Job (MavenWeb_Test)

Delete workspace

Copy artifacts from MavenWeb_Build

Maven goal: test

Post-build →

Archive artifacts

Build other project → MavenWeb_Deploy

Save.

Step 3: Create Deploy Job (MavenWeb_Deploy)

Delete workspace

Copy artifacts from MavenWeb_Test

Post-build → Deploy WAR

WAR: **/*.war

Context path: webpath

Container: Tomcat 9.x

Credentials: admin / 1234

URL: http://localhost:8085/

Save.

Step 4: Create Web Pipeline

Build Pipeline View →
Initial job: MavenWeb_Build

Step 5: Run & Verify

Click Run

If all green → success

Open Tomcat → Manager App

Login → click /webpath → project opens

-----------------------------------------------------------------------------------

Jenkins Pipeline – 

In Jenkins, create a new Pipeline project and give a description, then in Build Triggers select "Build periodically" and set the schedule to H/2 * * * * to run every 2 minutes. In the Pipeline section choose Pipeline script and paste the scripted pipeline. 
pipeline {
    agent any

    tools {
        maven 'MAVEN_HOME'
    }

    stages {
        stage('Clone Repo & Clean') {
            steps {
                
                bat 'rmdir /s /q maven-simple'

                
                bat 'git clone https://github.com/manishwarMoturi/maven-simple.git'

               
                bat 'mvn clean -f maven-simple/pom.xml'
            }
        }

        stage('Install') {
            steps {
                bat 'mvn install -f maven-simple/pom.xml'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test -f maven-simple/pom.xml'
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package -f maven-simple/pom.xml'
            }
        }
    }
}




---------------
1. Creation of Virtual Machine (Ubuntu EC2)

Login to AWS → go to EC2 → Launch Instance.
Give a name (e.g., ubuntu-server).
Select Ubuntu AMI (Free tier), 64-bit architecture, and t2.micro instance type.
Create a new key pair (.pem) and download it.
In Security Group, allow SSH (22), HTTP (80), and HTTPS (443).
Set storage to 8GB and launch the instance.
Then select your instance → click Connect → copy the SSH command.
In PowerShell/GitBash, go to the folder where the key is stored and run the SSH command to connect to the VM.


. Prepare the Server & Deploy the Application

Once connected to the Ubuntu VM, install required tools:

sudo apt update
sudo apt-get install docker.io
sudo apt install git
sudo apt install nano


Clone your Maven web project:

git clone <your-github-link>
cd <project-folder>


Create a Dockerfile:

nano Dockerfile


(Write the Dockerfile content, save and exit)

3. Build Docker Image

Inside the project folder, run:

docker build -t myapp .
docker images


This creates the Docker image for your web application.

4. Run Docker Container

Run your application container:

docker run -d --name app-demo -p 6060:8080 myapp


Container will run your web app on port 6060 of the VM.

5. Access the Application Publicly

Copy the public IP of your EC2 instance.
Open in a browser:

http://<public-ip>:6060


If it does not open, update Inbound Rules of the Security Group:
Add Custom TCP → Port 6060 → 0.0.0.0/0.
