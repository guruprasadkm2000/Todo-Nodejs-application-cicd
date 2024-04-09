
# An End-to-End Jenkins CI/CD Project

Deploy a Node.js Application on Docker using Jenkins and GitHub Integration.


## Installation

Install Java, Jenkins, Docker, Docker-Compose on AWS Ubuntu 22.04 
Java
```bash
sudo apt-get update
sudo apt install openjdk-11-jdk
java -version

```
Jenkins 
   
Download the Jenkins repository key and save it in a file using sudo
```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
   /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```
Add the Jenkins repository as a package for the package manager (apt) on your Ubuntu.  
```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
   https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
   /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install jenkins

```
Docker    
```bash
sudo apt-get update
sudo apt-get install docker.io
sudo systemctl status docker
sudo usermod -aG docker jenkins
```
docker-compose    
```bash
  sudo apt-get update
  sudo apt-get install docker-compose
  docker-compose version

```
## Steps

Open port 8080 to your system in the AWS console. Log in to the Jenkins dashboard and create a freestyle Pipeline named "todo-node-app"

Scroll down to Source Code Management on the Configure page and paste the Repository URL

Open port 8000 to the world for your system as they need to access the application


## create a dockerfile
```bash
sudo vim dockerfile
```

The dockerfile contents would look like this:
```bash
FROM node:12.2.0-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 8000
CMD ["node","app.js"]

```
Now in Jenkins go to Dashboard > Configure > Add Build Steps > Execute Shell. In the Command section, type the following command:

```bash
sudo docker build . -t todo-node-app
sudo docker run -d --name todo-node-app -p 8000:8000 todo-node-app

```
## The application is up and running. To access the application, use the following format of URL: public_IP_of_EC2:8080

All this was done manually. Let's automate this. We use Webhooks for GitHub for any events that take place in the repository, like a push or a commit. And Jenkins, as it is integrated with GitHub Webhooks, will automatically build the job.

let's install the GitHub Integration plugin in Jenkins, to integrate it with Webhooks. Go to Manage Jenkins > Manage Plugins > Available Plugins > Search for "GitHub Integration" > Select Install Without Restart

Configure GitHub Webhook in the meantime. In the repository settings > Go to Webhooks > Add Webhook

The Payload URL will be in the format: jenkins-URL/github-webhook

Change content type to application/json

Select Just the push event and Active. Click on Add Webhook.

If the URL is correct and accessible, after a refresh of the page, a webhook will be successfully added. A green tick would appear, besides the payload URL.

Now go to Jenkins > Dashboard > Job > Configure > Build Triggers > Select "GitHub hook trigger for GITScm polling"

Click on Save. Now change the code in GitHub a bit to see if the integration works.

To change the code go to repository > Open views > Open todo.js file > Edit line number 17 to "I am performing Jenkins with GitHub Integration! I am completing an End to End Projects!"

Commit changes.

## Build starts on its own


## To Stop and Remove Containers
```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```
    
