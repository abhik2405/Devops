# Jenkins CI/CD with GitHub Integration

## Pre-requisities:

* AWS Account
* GitHub Account
* Basic Knowledge about Docker & Jenkins

### Steps:-



#### 1. Launch an EC2 Ubuntu Instance with access of HTTP & HTTPS
#### 2. Install Jenkins on Ubuntu Machine
#### 3. Setup Jenkins & Connect with GIT by Installing Github Integration
#### 4. Finally Enable the Jenkins Script for CI/CD of Docker Provision Node.js
#### 5. Install Github integration plugin
#### 6. Webhook Configuration



### Step 1:

***Go to your AWS Account & Launch an Ubuntu Instance
Open EC2 -> Instances --> Launch an EC2 Instance
Select Ubuntu Image***

![image](https://user-images.githubusercontent.com/84725860/210175095-58852d0b-fbdf-431f-9991-5d12fd35647f.png)

***Provide Following Details :***

Name = Jenkins-Project-1

Instance Type = t2.micro

Allow HTTP & HTTPS Traffic From Internet

Creat New Key Pair

Once Done Launch Your EC2 Instance

Note: Please Create a new key pair or use existing one to login


### Step 2: 

***Connect to your EC2 Instance to Install Jenkins***

Run below Commands One by One

Update Your System

```
sudo apt update
```

Install JAVA

```
sudo apt install openjdk-11-jre
```

Check JAVA Version by running below command

```
java -version
```

Now Install Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null
```

```
sudo apt-get update 
```

```
sudo apt-get install jenkins
```

Once Done Enable & Start the Jenkins Service


```
sudo systemctl enable jenkins
```

Starts the Jenkins Service

```
sudo systemctl start jenkins
```

Check the Service Status

```
sudo systemctl status jenkins
```
Now Goto Security Group of your EC2 Instance & provide below Inbound Rules & Save Changes

![image](https://user-images.githubusercontent.com/84725860/210175715-6523bf2e-306d-4e0c-bf21-cdfe23b48500.png)

Now Open your Jenkins Server by Below Address

```
http:// [public-ip]:8080 /
```

You can see below screen 

![image](https://user-images.githubusercontent.com/84725860/210175757-b4d812cf-0c5b-43d4-ae82-58a6f014a113.png)

### Step 3:

***Now Locate your Jenkins Administrator password by command***

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```


Enter that password & select Install Suggested Plugins Once Done Provide the Necessary Details & click on Save & Continue


Check the Jenkins URL & click on Save & Finish


Now Click on Start Using Jenkins you can see below screen 

![image](https://user-images.githubusercontent.com/84725860/210176171-23f7895e-150f-41fc-a8d9-c4174a5e4b97.png)

Provide Item Name, we are using freestyle pipeline so choose freestyle project

![image](https://user-images.githubusercontent.com/121798037/210228314-4f6f67c9-632d-4d03-b7b7-ca12eacfa6cf.png)

Once Done Click on save

Before Configuring lets connect Jenkins with GIT using SSH


Go to your EC2 Instance & run below command

```
ssh-keygen
```

It will generate public & private key provide public key to GitHub & provide private key for Jenkins
Access the keys changing the directory to

```
cd .ssh
ls
```


Now Go to your GitHub & provide the public key as

```
cat id_rsa.pub
```

![image](https://user-images.githubusercontent.com/84725860/210176867-19c1cbd6-9ce6-4e91-aa26-007a3341c548.png)

Copy that key and add to your GitHub SSH Keys Section


As you can see i have added the SSH key for the GitHub
Now similarly add private key to your Jenkins
Go to your Project -->  Configure

Check Github Project & Provide the Project URL

![image](https://user-images.githubusercontent.com/121798037/210234023-a2311a9e-dcca-416f-b473-6d1f54959b18.png)

In Source Code Management select GIT and paste the repository URL 

![image](https://user-images.githubusercontent.com/121798037/210234091-d68ce88b-430d-4148-8fd0-c573bc4b473b.png)

Now in credentials click on add

Provide the Details

In kind select SSH with Private Key

Username select as Ubuntu  -->  Username of EC2 Instance

In Private key select Entire Directly & paste your Private Key as we copied public key

```
cat id_rsa
```

Once Done Check Specifier For me it's main

![image](https://user-images.githubusercontent.com/84725860/210177246-cf8a51c2-3679-4d15-be93-bb5a07515762.png)

Now click on Save

After that click on Build Now

You can see build is started Once Done open that build 

Go to console output & copy the address

![image](https://user-images.githubusercontent.com/121798037/210234236-f55a1099-5a5a-4530-9480-bd35f6b62e56.png)

Now open your Instance & change Directory with

```
cd /var/lib/jenkins/workspace/Item-Name  (abhishek is my Item Name)
```

Installing Node. js and npm from NodeSource

```
sudo apt install nodejs sudo apt install npm
```

Install the  requirement for .json

```
npm install
```
Install dependencies and then start the app

```
node app.js
```

Now Your App is running on 8000 Port so give the inbound accesss:

![image](https://user-images.githubusercontent.com/121798037/210231166-cab955e8-4d8c-4d88-ac81-6dfc7db36523.png)

![image](https://user-images.githubusercontent.com/121798037/210231423-82556e79-3d1b-4f08-894d-23e0a5894619.png)

Type the Public ip with 8000 Port:- 

![image](https://user-images.githubusercontent.com/121798037/210234344-93172271-07dc-4741-b639-e61fa4e4da43.png)

Now lets install docker and build the docker image by following commands 

```
sudo apt install docker.io
```

Now creat the docker file 

```
vim dockerfile
```

![image](https://user-images.githubusercontent.com/84725860/210178076-2534550d-ea81-46d7-ad7e-cd1e4e48aef3.png)

Give the commands in that docker file

Install Alpine Linux image with node 12.2.0 

```
From node:12.2.0-alpine
```
Define the working directory

```
WORKDIR app
```
Copy all the files from the project's root to /app

```
COPY . .
```
Install node_modules

```
RUN npm install
```
This tells Docker your webserver will listen on port 8080

```
EXPOSE 8080
```
CMD command specifies the instruction that is to be executed when a Docker container starts

```
CMD ["node","app.json"]
```

![image](https://user-images.githubusercontent.com/121798037/210257452-4c5ad2a0-d678-4cce-b859-b90734b63f7d.png)

Add your user to the docker group / If you want to be able to skip sudo for docker commands

```
sudo usermode -a -G docker $USER
```

Reboot the docker user

```
sudo reboot
```

Launch the instance and got to Build path

Once Rebooted build Image by following Command 

```
sudo docker build . -t todo-job1
```
After Successfully image is built run the image by

```
sudo docker run -d  --name todo-job1 -p 8000:8000 todo-job1
```
Here Container-name --> todo , -d --> detached mode , -p -->Expose port 8000

To check the state of Docker

```
docker ps
```

### Step 4:

***Now lets Run the Docker through Jenkins***

Now let,s run the Docker through jenkins

Give full access to build the path (abhishek is job name)
```
sudo chmod 777 /var/lib/jenkins/workspace/abhishek 
```
Give access to the Docker daemon socket
```
sudo usermod -a -G docker jenkins
```
Restart Your jenkins 
```
sudo systemctl restart jenkins

```
Then add build steps in execute shell inside jenkins 

![image](https://user-images.githubusercontent.com/121798037/210250737-edab54ba-52bb-446a-8159-2ed6e9f16e56.png)
 
 Then check port 8000 whether app is working on that port or not
 
 ![image](https://user-images.githubusercontent.com/121798037/210250920-97da63da-4368-4f0e-8525-0806ba654892.png)

### Step5 

***Install github integration plugin***

![image](https://user-images.githubusercontent.com/121798037/210251206-698a0f38-13e1-40bb-a4b5-75e62ed2e3a5.png)

### Step 6 

***Webhook configuration***

In git hub we must have SSH key 

then go to settings of  created repo

after that click on webhook and add webhook

![image](https://user-images.githubusercontent.com/121798037/210253226-193711a5-2ca7-479f-b2ee-c27e23e612dc.png)

then enter  jenkins payload URL

![image](https://user-images.githubusercontent.com/121798037/210253895-5746bbd5-2564-4cc5-92b4-599fa587319e.png)

Once Webhook createds successfully go to jenkins and edit configuration for Build Triggers

![image](https://user-images.githubusercontent.com/121798037/210254526-b3fdcd0a-a804-4af5-ad86-1756228aee3e.png)

Once all setup is Done if you commit any new change in github 

New build will automatically start in Jenkins

![image](https://user-images.githubusercontent.com/121798037/210255200-255d99ec-a2fc-4b7f-a171-1972b47ec74b.png)


# THANK YOU





