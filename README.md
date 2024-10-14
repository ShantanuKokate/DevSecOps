# DevSecOps
This project involves deploying a Netflix-like application using advanced CI/CD practices to achieve efficient integration, delivery, and monitoring. The pipeline leverages key technologies, including Jenkins for automation, Prometheus for monitoring, and Grafana for visualization, ensuring a robust and scalable architecture. By implementing GitOps with ArgoCD, the application maintains alignment with its source code, enabling seamless updates and rollbacks. Overall, this deployment exemplifies a modern, reliable approach to application management.

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
Integrate SonarQube and Configure:
• Integrate SonarQube with your CI/CD pipeline.
• Configure SonarQube to analyze code for quality and security issues.
### Phase 3: CI/CD Setup

• **Install Jenkins for Automation:**  
  Install Jenkins on the EC2 instance to automate deployment.

• **Install Java:**  
  Run the following commands to install Java:

  ```bash
  sudo apt update  
  sudo apt install fontconfig openjdk-17-jre  
  java -version  

  #jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
Access Jenkins:
• Access Jenkins in a web browser using the public IP of your EC2 instance:

plaintext
Copy code
publicIp:8080

### Install Necessary Plugins in Jenkins:

• **Go to Manage Jenkins → Plugins → Available Plugins:**  
  Install the following plugins:

  1. Eclipse Temurin Installer (Install without restart)  
  2. SonarQube Scanner (Install without restart)  
  3. NodeJS Plugin (Install without restart)  
  4. Email Extension Plugin  

• **Configure Java and Node.js in Global Tool Configuration:**  
  Go to Manage Jenkins → Tools → Install JDK (17) and Node.js (16) → Click on Apply and Save.

### SonarQube:

• **Create the Token:**  
  Go to Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text.  
  It should look like this:

  *After adding the Sonar token, click on Apply and Save.*

• **Configure System Option:**  
  The Configure System option is used in Jenkins to configure different servers.  

• **Global Tool Configuration:**  
  This is used to configure different tools that we install using Plugins.  
  We will install a Sonar scanner in the tools.

• **Create a Jenkins Webhook:**  

### Configure CI/CD Pipeline in Jenkins:

• **Create a CI/CD Pipeline to automate your application deployment:**  

  ```groovy
  pipeline {
      agent any
      tools {
          jdk 'jdk17'
          nodejs 'node16'
      }
      environment {
          SCANNER_HOME = tool 'sonar-scanner'
      }
      stages {
          stage('clean workspace') {
              steps {
                  cleanWs()
              }
          }
          stage('Checkout from Git') {
              steps {
                  git branch: 'main', url: 'https://github.com/N4si/DevSecOps-Project.git'
              }
          }
          stage("Sonarqube Analysis") {
              steps {
                  withSonarQubeEnv('sonar-server') {
                      sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                      -Dsonar.projectKey=Netflix'''
                  }
              }
          }
          stage("quality gate") {
              steps {
                  script {
                      waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                  }
              }
          }
          stage('Install Dependencies') {
              steps {
                  sh "npm install"
              }
          }
      }
  }
```
### Install Dependency-Check and Docker Tools in Jenkins:

• **Install Dependency-Check Plugin:**  
  - Go to "Dashboard" in your Jenkins web interface.  
  - Navigate to "Manage Jenkins" → "Manage Plugins."  
  - Click on the "Available" tab and search for "OWASP Dependency-Check."  
  - Check the checkbox for "OWASP Dependency-Check" and click on the "Install without restart" button.  

• **Configure Dependency-Check Tool:**  
  After installing the Dependency-Check plugin, you need to configure the tool.  
  - Go to "Dashboard" → "Manage Jenkins" → "Global Tool Configuration."  
  - Find the section for "OWASP Dependency-Check."  
  - Add the tool's name, e.g., "DP-Check."  
  - Save your settings.  

• **Install Docker Tools and Docker Plugins:**  
  - Go to "Dashboard" in your Jenkins web interface.  
  - Navigate to "Manage Jenkins" → "Manage Plugins."  
  - Click on the "Available" tab and search for "Docker."  
  - Check the following Docker-related plugins:  
    - Docker  
    - Docker Commons  
    - Docker Pipeline  
    - Docker API  
    - docker-build-step  
  - Click on the "Install without restart" button to install these plugins.  

• **Add DockerHub Credentials:**  
  To securely handle DockerHub credentials in your Jenkins pipeline, follow these steps:  
  - Go to "Dashboard" → "Manage Jenkins" → "Manage Credentials."  
  - Click on "System" and then "Global credentials (unrestricted)."  
  - Click on "Add Credentials" on the left side.  
  - Choose "Secret text" as the kind of credentials.  
  - Enter your DockerHub credentials (Username and Password) and give the credentials an ID (e.g., "docker").  
  - Click "OK" to save your DockerHub credentials.  

### Phase 4: Monitoring

• **Install Prometheus and Grafana:**  
  Set up Prometheus and Grafana to monitor your application.

• **Installing Prometheus:**  
  First, create a dedicated Linux user for Prometheus and download Prometheus:

  ```bash
  sudo useradd --system --no-create-home --shell /bin/false prometheus  
  wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz  
```
Extract Prometheus files, move them, and create directories:
```bash 
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz  
cd prometheus-2.47.1.linux-amd64/  
sudo mkdir -p /data /etc/prometheus  
sudo mv prometheus promtool /usr/local/bin/  
sudo mv consoles/ console_libraries/ /etc/prometheus/  
sudo mv prometheus.yml /etc/prometheus/prometheus.yml  
```
• Create a systemd unit configuration file for Prometheus:

```bash
sudo nano /etc/systemd/system/prometheus.service
```
Add the following content to the prometheus.service file:

```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```
### Key Parts of the `prometheus.service` File

- **User and Group**:  
  Specify the Linux user and group under which Prometheus will run.

- **ExecStart**:  
  Specify the Prometheus binary path, the location of the configuration file (`prometheus.yml`), the storage directory, and other settings.

- **web.listen-address**:  
  Configures Prometheus to listen on all network interfaces on port 9090.

- **web.enable-lifecycle**:  
  Allows for management of Prometheus through API calls.

Enable and start Prometheus:
```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```
Verify Prometheus's status:
```bash
sudo systemctl status prometheus
```
You can access Prometheus in a web browser using your server's IP and port 9090:

http://<your-server-ip>:9090

Installing Node Exporter:

Create a system user for Node Exporter and download Node Exporter:
```bash
sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
```
Extract Node Exporter files, move the binary, and clean up:
```bash
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*
```
Create a systemd unit configuration file for Node Exporter:
```bash
sudo nano /etc/systemd/system/node_exporter.service
```
sudo nano /etc/systemd/system/node_exporter.service
```bash
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target
```
####Grafana

Install Grafana on Ubuntu 22.04 and Set it up to Work with Prometheus

Step 1: Install Dependencies:

First, ensure that all necessary dependencies are installed:
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https software-properties-common
```
Step 2: Add the GPG Key:

Add the GPG key for Grafana:
```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```
Step 3: Add Grafana Repository:

Add the repository for Grafana stable releases:
```bash
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
Step 4: Update and Install Grafana:

Update the package list and install Grafana:
```bash
sudo apt-get update
sudo apt-get -y install grafana
```
Step 5: Enable and Start Grafana Service:

To automatically start Grafana after a reboot, enable the service:
```bash
sudo systemctl enable grafana-server
```
Then, start Grafana:
```bash
sudo systemctl start grafana-server
```
Step 6: Check Grafana Status:

Verify the status of the Grafana service to ensure it's running correctly:
```bash
sudo systemctl status grafana-server
```
Step 7: Access Grafana Web Interface:

Open a web browser and navigate to Grafana using your server's IP address. The default port for Grafana is 3000. For example:

http://<your-server-ip>:3000

You'll be prompted to log in to Grafana. The default username is "admin," and the default password is also "admin."

Step 8: Change the Default Password:

When you log in for the first time, Grafana will prompt you to change the default password for security reasons. Follow the prompts to set a new password.

Step 9: Add Prometheus Data Source:

To visualize metrics, you need to add a data source. Follow these steps:

Click on the gear icon (⚙️) in the left sidebar to open the "Configuration" menu.

Select "Data Sources."

Click on the "Add data source" button.

Choose "Prometheus" as the data source type.

In the "HTTP" section:

Set the "URL" to http://localhost:9090 (assuming Prometheus is running on the same server).
Click the "Save & Test" button to ensure the data source is working.
Step 10: Import a Dashboard:

To make it easier to view metrics, you can import a pre-configured dashboard. Follow these steps:

Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.

Select "Dashboard."

Click on the "Import" dashboard option.

Enter the dashboard code you want to import (e.g., code 1860).

Click the "Load" button.

Select the data source you added (Prometheus) from the dropdown.

Click on the "Import" button.

You should now have a Grafana dashboard set up to visualize metrics from Prometheus.

Grafana is a powerful tool for creating visualizations and dashboards, and you can further customize it to suit your specific monitoring needs.

That's it! You've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.

Configure Prometheus Plugin Integration:
Integrate Jenkins with Prometheus to monitor the CI/CD pipeline.
Phase 5: Notification

Implement Notification Services:
Set up email notifications in Jenkins or other notification mechanisms.
Phase 6: Kubernetes
Create Kubernetes Cluster with Nodegroups
In this phase, you'll set up a Kubernetes cluster with node groups. This will provide a scalable environment to deploy and manage your applications.

Monitor Kubernetes with Prometheus
Prometheus is a powerful monitoring and alerting toolkit, and you'll use it to monitor your Kubernetes cluster. Additionally, you'll install the node exporter using Helm to collect metrics from your cluster nodes.

Install Node Exporter using Helm
To begin monitoring your Kubernetes cluster, you'll install the Prometheus Node Exporter. This component allows you to collect system-level metrics from your cluster nodes. Here are the steps to install the Node Exporter using Helm:

Add the Prometheus Community Helm repository:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
Create a Kubernetes namespace for the Node Exporter:

kubectl create namespace prometheus-node-exporter
Install the Node Exporter using Helm:

helm install prometheus-node-exporter prometheus-community/prometheus-node-exporter --namespace prometheus-node-exporter
Add a Job to Scrape Metrics on nodeip:9001/metrics in prometheus.yml:

Update your Prometheus configuration (prometheus.yml) to add a new job for scraping metrics from nodeip:9001/metrics. You can do this by adding the following configuration to your prometheus.yml file:

  - job_name: 'Netflix'
    metrics_path: '/metrics'
    static_configs:
      - targets: ['node1Ip:9100']
Replace 'your-job-name' with a descriptive name for your job. The static_configs section specifies the targets to scrape metrics from, and in this case, it's set to nodeip:9001.

Don't forget to reload or restart Prometheus to apply these changes to your configuration.

To deploy an application with ArgoCD, you can follow these steps, which I'll outline in Markdown format:

### Deploy Application with ArgoCD

1. **Install ArgoCD:**  
   You can install ArgoCD on your Kubernetes cluster by following the instructions provided in the **EKS Workshop documentation.**

---

2. **Set Your GitHub Repository as a Source:**  
   After installing ArgoCD, you need to set up your GitHub repository as a source for your application deployment. This typically involves:

   - Configuring the connection to your repository.
   - Defining the source for your ArgoCD application.  
   The specific steps will depend on your setup and requirements.
### Create an ArgoCD Application

6. **Define Your Application Configuration:**  
   Specify the following parameters for your application:

   - **name:** Set the name for your application.
   - **destination:** Define the destination where your application should be deployed.
   - **project:** Specify the project the application belongs to.
   - **source:** Set the source of your application, including the GitHub repository URL, revision, and the path to the application within the repository.
   - **syncPolicy:** Configure the sync policy, including automatic syncing, pruning, and self-healing.

---

7. **Access Your Application:**  
   To access the app, make sure port **30007** is open in your security group, and then open a new tab and paste `http://<NodeIP>:30007`. Your app should be running.

---

### Phase 7: Cleanup

8. **Cleanup AWS EC2 Instances:**  
   Terminate AWS EC2 instances that are no longer needed.









