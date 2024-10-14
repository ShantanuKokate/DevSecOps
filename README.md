# DevSecOps
Netflix application deployment

![DevSecOps](https://github.com/ShantanuKokate/DevSecOps/blob/main/public/assets/DevSecOps.png?raw=true)

![Home Page](https://github.com/ShantanuKokate/DevSecOps/blob/main/public/assets/home-page.png?raw=true)

# Deploy Netflix Clone on Cloud using Jenkins - DevSecOps Project!

## Phase 1: Initial Setup and Deployment

### Step 1: Launch EC2 (Ubuntu 22.04):

• Provision an EC2 instance on AWS with Ubuntu 22.04.  
• Connect to the instance using SSH.

### Step 2: Clone the Code:

• Update all the packages and then clone the code.  

• Clone your application's code repository onto the EC2 instance:
### Step 2: Clone the Code:

• Update all the packages and then clone the code.  

• Clone your application's code repository onto the EC2 instance:

```bash
git clone https://github.com/ShantanuKokate/DevSecOps.git
sudo apt-get update  
sudo apt-get install docker.io -y  
sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'  
newgrp docker  
sudo chmod 777 /var/run/docker.sock  
```
### Build and Run Your Application Using Docker Containers:

• Build the Docker image for your application and run the Docker container:  
```bash
docker build -t netflix .  
docker run -d --name netflix -p 8081:80 netflix:latest

#to delete
docker stop <containerid>
docker rmi -f netflix
```
### Step 4: Get the API Key:

• Open a web browser and navigate to the TMDB (The Movie Database) website.  
• Click on "Login" and create an account.  
• Once logged in, go to your profile and select "Settings."  
• Click on "API" from the left-side panel.  
• Create a new API key by clicking "Create" and accepting the terms and conditions.  
• Provide the required basic details and click "Submit."  
• You will receive your TMDB API key.
### Recreate the Docker Image with Your API Key:

• Now recreate the Docker image with your API key:  
```bash
docker build --build-arg TMDB_V3_API_KEY=<your-api-key> -t netflix .
```
### Phase 2: Security

• Install SonarQube and Trivy:  
• Install SonarQube and Trivy on the EC2 instance to scan for vulnerabilities.  

• To run SonarQube, use the following command:  
  ```bash
  docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
### To Install Trivy:

• Run the following commands to install Trivy:  
  ```bash
  sudo apt-get install wget apt-transport-https gnupg lsb-release  
  wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -  
  echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list  
  sudo apt-get update
```

 ### To Scan an Image Using Trivy:

• Use the following command to scan an image:  
  ```bash
  trivy image <imageid>
  sudo apt-get install trivy  
```



