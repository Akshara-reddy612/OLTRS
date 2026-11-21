
Configure Jenkins
You need to enable the GitHub trigger.
1. Open Jenkins dashboard.
2. Select your Pipeline job.
3. Click Configure.
4. Find Build Triggers.
5. Select GitHub hook trigger for GITScm polling.
6. Save.

next
Configure GitHub Webhook
Open your GitHub repository.
1. Go to Settings.
2. Select Webhooks.
3. Click Add webhook.
//next
Field	Value
Payload URL	http://localhost:8080/github-webhook/
Content type	application/json
Secret	Optional, configure securely
Events	Just the push event
Active	Checked

How to test
1. Make a small change to a file in your GitHub repository.
2. Commit and push the change.
3. GitHub sends a webhook.
4. Jenkins automatically starts the pipeline.
5. Check Jenkins → Build History → Console Output.


Start Minikube and deploy the application using its Docker image.
# Start Minikube
minikube start --driver=docker

# Check Minikube status
minikube status

# Create deployment using Nginx image
kubectl create deployment food-management --image=nginx:latest

# Check deployments
kubectl get deployments

# Check pods
kubectl get pods

#Scale Kubernetes Deployment to 4 Replicas
kubectl scale deployment food-management --replicas=4

# Describe deployment
kubectl describe deployment food-management



# Scale deployment to 4 replicas
kubectl scale deployment food-management --replicas=4

# Check deployment
kubectl get deployments

# Check pods
kubectl get pods

# View deployment details
kubectl describe deployment food-management


Configure a NodePort service to expose the application and verify its accessibility through a browser

# Expose deployment using NodePort
kubectl expose deployment food-management --type=NodePort --port=80

# Check services
kubectl get services

# Get application URL
minikube service food-management --url

# Describe service
kubectl describe service food-management

# Check pods
kubectl get pods

Noagios

Download the Nagios Docker image.
Create and run a container.
Map the dashboard port.
Open the dashboard in a browser.
Observe host and service status.




# Pull Nagios image
docker pull jasonrivers/nagios:latest

# Run Nagios container
docker run -d --name nagios -p 8081:80 jasonrivers/nagios:latest

# Check running container
docker ps

# Check container logs
docker logs nagios

# Stop container
docker stop nagios

# Start container again
docker start nagios

# Remove container
docker rm -f nagios

Identify the possible cause and troubleshoot the deployment using Kubernetes commands

# Check Pods
kubectl get pods

# Describe problematic Pod
kubectl describe pod POD_NAME

# Check Kubernetes events
kubectl get events --sort-by=.metadata.creationTimestamp

# Check nodes
kubectl get nodes

# Describe node resources
kubectl describe nodes

# Check resource usage (if Metrics Server is installed)
kubectl top nodes

# Check deployments
kubectl get deployments

# Describe deployment
kubectl describe deployment food-management

# Restart deployment if required
kubectl rollout restart deployment food-management

# Verify rollout
kubectl rollout status deployment food-management


Create Ubuntu EC2 instance
Open AWS EC2 Console.

Follow these steps:
1. Click Launch instance.
2. Enter instance name: Food-Management.
3. Select AMI: Ubuntu Server 24.04 LTS.
4. Instance type: t2.micro or an eligible free-tier instance type available in your account.
5. Create or select a key pair.
6. Under Network settings, configure the security group. 
Type	Port	Source
SSH	22	My         IP
HTTP	80	    Anywhere (0.0.0.0/0) 
7.Click Launch instance.
8.Wait until instance state shows Running and status checks pass.

Connect to EC2
In the EC2 dashboard:
1. Select your instance.
2. Click Connect.
3. Select EC2 Instance Connect.
4. Click Connect.


 # Update Ubuntu
sudo apt update

# Install Docker
sudo apt install docker.io -y

# Start Docker
sudo systemctl start docker

# Enable Docker
sudo systemctl enable docker

# Verify Docker
sudo docker --version

# Create project directory
mkdir food
cd food

# Create HTML file
nano index.html(

<!DOCTYPE html>
<html>
<head>
    <title>Food Management System</title>
</head>
<body>
    <h1>Welcome to Food Management System!</h1>
    <p>Website successfully deployed using AWS EC2, Docker and Nginx.</p>
</body>
</html>


)

# Run Nginx container
sudo docker run -d --name food-container -p 80:80 -v "$PWD/index.html:/usr/share/nginx/html/index.html:ro" nginx:latest

# Check running containers
sudo docker ps


8. Open website in browser
Go to AWS EC2 Console → Select your instance → Copy its Public IPv4 address.
http://YOUR_PUBLIC_IP 

Step 1: Open AWS EC2 Console
1. Go to AWS EC2 Console.
2. Click Instances from the left sidebar.
3. Select your Ubuntu EC2 instance.
4. Make sure Instance state is Running.
5. Click Connect.
Select the EC2 Instance Connect tab.
Keep the default username ubuntu.
Click Connect.
A new browser tab opens with a black Ubuntu terminal. 
Step 3: Enter commands one by one-->


 Connect to AWS EC2 using SSH, Clone GitHub Repository and Build Docker Image.
Open Windows PowerShell on your laptop.
ssh -i "YOUR_KEY_PATH.pem" ubuntu@YOUR_PUBLIC_IP
Replace:
- YOUR_KEY_PATH.pem with the location of your downloaded EC2 key pair.
- YOUR_PUBLIC_IP with your EC2 instance's Public IPv4 address.
 


Install Git and Docker
Enter these commands inside the EC2 Ubuntu terminal:
# Update Ubuntu
sudo apt update

# Install Git and Docker
sudo apt install git docker.io -y

# Start Docker
sudo systemctl start docker

# Clone repository
git clone YOUR_GITHUB_REPOSITORY_URL

# Enter project folder
cd YOUR_PROJECT_FOLDER

# Create Dockerfile
nano Dockerfile

file content:
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .

COPY src ./src

RUN mvn clean package -DskipTests

FROM tomcat:9.0-jdk17-temurin

COPY --from=build /app/target/*.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080

CMD ["catalina.sh", "run"]


# Build Docker image
sudo docker build -t food-management:latest .

# Verify Docker image
sudo docker images 

#Run container on port 9090
sudo docker run -d --name food-container -p 9090:8080 food-management:latest

7. Verify container
sudo docker ps


Configure AWS Security Group
Go to:

AWS Console → EC2 → Instances → Select instance → Security → Security Groups → Edit inbound rules.
Add:
Type	Port	Source
SSH	22	        My  IP
HTTP	80	    Anywhere
Custom TCP	9090 Anywhere (0.0.0.0/0)

9. Access application in browser
Copy your EC2 Public IPv4 address.
**http://YOUR_PUBLIC_IP:9090**

Replace YOUR_PUBLIC_IP with your EC2 public IPv4 address. -->


 Configure the EC2 security group's inbound rules to allow application traffic on port 9090 and verify website accessibility using the public IPv4 address. 

follow these steps:
1. Click Instances.
2. Select your Ubuntu EC2 instance.
3. Open the Security tab.
4. Click the attached Security Group.
5. Click Edit inbound rules.
6. Click Add rule.


Add inbound rule
Enter these details:
Inbound rule configuration
Type -1

Custom TCP -1


Protocol -2

TCP -2


Port range

9090


Source

Anywhere IPv4

Click Save rules.

Connect to your EC2 Ubuntu instance using EC2 Instance Connect or SSH.

Execute:

# Check Docker container
sudo docker ps

# Start container if stopped
sudo docker start food-container

# Test locally on EC2
curl http://localhost:9090


If your application is working, you should receive HTML output.
6. Access website using Public IPv4
1. Go to AWS EC2 → Instances.
2. Select your instance.
3. Copy its Public IPv4 address.
4. Open Chrome.
Final website URL
http://YOUR_PUBLIC_IP:9090
Replace YOUR_PUBLIC_IP with your EC2 instance's actual public IPv4 address.

Your Food Management System should now be accessible!




 Stop the Docker container and terminate the EC2 instance after testing
Step 1: Connect to your EC2 instance
Open AWS Console → EC2 → Instances → Select your instance → Connect → EC2 Instance Connect

Check running containers
Run this in the Ubuntu EC2 terminal:

sudo docker ps

To see both running and stopped containers:
sudo docker ps -a

Step 3: Stop the Docker container
If your container name is food-container, run:
sudo docker stop food-container

sudo docker ps

Step 4: Remove the stopped container (optional cleanup)
sudo docker rm food-container

Step 5: Terminate the EC2 instance

Go to EC2 → Instances.
Select the EC2 instance you created for this practical.
Click Instance state.
Select Terminate instance.
Confirm by clicking Terminate 



 Run Docker Compose
Execute these commands in the project terminal:

Step 1: Build images and start containers
docker compose up -d --build


Step 2: Check running containers
docker ps


Expected result: Both flask-container and nginx-container should be running.

Step 3: Check logs
docker compose logs


Step 4: Access applications
Flask application
5000

Open in browser:

http://localhost:5000

Expected: Welcome to Food Management System!

Nginx web server
80

Open in browser:

http://localhost

Expected: Nginx welcome page

Step 5: Stop and remove containers
docker compose down 
Flask: http://localhost:5001
Nginx: http://localhost:8080


docker compose up -d --build
docker ps
docker compose logs
docker compose down
