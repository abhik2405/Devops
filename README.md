# Jenkins CI/CD with GitHub Integration

## Pre-requisities:

* AWS Account
* GitHub Account
* Basic Knowledge about Docker & Jenkins

### Steps:-

#### 1. Launch an EC2 Ubuntu Instance with access of HTTP & HTTPS

</br>
<kbd align="center"><img src="Images/1.jpg"/></kbd>
</br>

#### 2. Launch an EC2 Ubuntu Instance with access of HTTP & HTTPS
#### 3. Install Jenkins on Ubuntu Machine
#### 4. Setup Jenkins & Connect with GIT by Installing Github Integration
#### 5. Finally Enable the Jenkins Script for CI/CD of Docker Provision Django Application



### Step 1:

Go to your AWS Account & Launch an Ubuntu Instance
Open EC2 -> Instances --> Launch an EC2 Instance
Select Ubuntu Image

![image](https://user-images.githubusercontent.com/84725860/210175095-58852d0b-fbdf-431f-9991-5d12fd35647f.png)

Provide Following Details :

Name = Jenkins-Project-1

Instance Type = t2.micro

Allow HTTP & HTTPS Traffic From Internet

Creat New Key Pair

Once Done Launch Your EC2 Instance

Note: Please Create a new key pair or use existing one to login


### Step 2: 

Connect to your EC2 Instance to Install Jenkins 
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


### Step 4:

Now Locate your Jenkins Administrator password by command


```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```


Enter that password & select Install Suggested Plugins Once Done Provide the Necessary Details & click on Save & Continue

Check the Jenkins URL & click on Save & Finish

Now Click on Start Using Jenkins you can see below screen 

![image](https://user-images.githubusercontent.com/84725860/210176171-23f7895e-150f-41fc-a8d9-c4174a5e4b97.png)

Provide Item Name, we are using freestyle pipeline so choose freestyle project

![image](https://user-images.githubusercontent.com/84725860/210176217-33394a71-17a7-4857-9b94-79e6c3f43392.png)

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

![image](https://user-images.githubusercontent.com/84725860/210176794-8781f3d2-481d-45d2-b837-728701cde1e4.png)

In Source Code Management select GIT and paste the repository URL 

![image](https://user-images.githubusercontent.com/84725860/210176808-a6e4023d-0322-40fc-830b-c72473590105.png)

Now in credentials click on add

Provide the Details
In kind select SSH with Private Key
Username select as Ubuntu  -->  Username of EC2 Instance

In Private key select Entire Directly & paste your Private Key as we copied public key

```
cat id_rsa
```
</br>
<kbd align="center"><img src="Images/10.jpg"/></kbd>
</br>

Once Done Check Specifier For me it's main

![image](https://user-images.githubusercontent.com/84725860/210177246-cf8a51c2-3679-4d15-be93-bb5a07515762.png)

Now click on Save

After that click on Build Now

You can see build is started Once Done open that build 

Go to console output & copy the address

![image](https://user-images.githubusercontent.com/84725860/210177287-796a10d8-35c5-417c-bd7a-988f787a572e.png)

Now open your Instance & change Directory with

```
cd /var/lib/jenkins/workspace/Item-Name
```

```
sudo apt install nodejs sudo apt install npm
~~~
Install the  requirement for .json
```
npm install
```
node app.js
```
Now Your App is running on 8000 Port so give the inbound accesss:

![image](https://user-images.githubusercontent.com/84725860/210177628-90d0e1f1-094c-4edc-82f2-b74e57172da3.png)

![image](https://user-images.githubusercontent.com/84725860/210177652-d210d67f-c09f-4167-9eb9-fde2b8b6aa66.png)


Type the Public ip with 8000 Port:- 

![image](https://user-images.githubusercontent.com/84725860/210177770-73b6bbe5-5f55-44c1-9b09-05d4b6b52b8e.png)


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

```
From node:12.2.0-alpine
```
```
WORKDIR app
```
```
COPY . .
```
```
RUN npm install
```
```
EXPOSE 8080
```
```
CMD ["node","app.json"]

![image](https://user-images.githubusercontent.com/84725860/210178326-c5bdae65-b710-42c5-8c7d-8725225df829.png)

Give the all Permisssion to docker user

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

Now lets Run the Docker through Jenkins

![image](https://user-images.githubusercontent.com/84725860/210182648-7e985832-03bb-4d64-93ff-81b3adfb1b22.png)







Go to 
