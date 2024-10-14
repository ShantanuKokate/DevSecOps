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

