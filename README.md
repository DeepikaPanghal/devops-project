# DevOps Lifecycle Project: Scalable Container Platform

## Overview

This project simulates a real-world scenario where I was hired as a DevOps engineer at a product-based company (Analytics Pvt Ltd). The goal was to build a robust DevOps lifecycle using Docker, Kubernetes, Jenkins, and Terraform to automate deployment, scaling, and operations of containerized applications without modifying the Docker containers used in the testing environment.

## Tools Used
- **Docker** for containerization
- **Kubernetes** for orchestration and scaling
- **Jenkins** for CI/CD
- **Terraform** for infrastructure provisioning

## Scenario

> The organization experienced rapid growth in demand for their product and required a scalable deployment platform. The existing system followed a monolithic architecture with only 2 developers. My task was to implement a complete DevOps lifecycle enabling CI/CD, infrastructure-as-code, and container orchestration.

Full scenario details can be found in [docs/project-overview.md](docs/project-overview.md)

## How to Use

Instructions for running the project locally or deploying via Jenkins + Terraform...

How to Structure the Repository
Here's a recommended folder and file structure:

📁 devops-lifecycle-k8s-docker-terraform-jenkins/
├── README.md
├── docker/
│   └── Dockerfile/             # Sample Dockerized application
├── jenkins/
│   └── Jenkinsfile             # CI/CD pipeline configuration
├── kubernetes/
│   ├── deployment.yaml         # K8s Deployment config
│   ├── service.yaml            # Service definition
│   └── ingress.yaml            # Ingress (optional)
├── terraform/
│   └── main.tf                 # Infrastructure provisioning code










DevOps Project: CI/CD Pipeline with Terraform, Ansible, Jenkins, Docker, and Kubernetes
This project demonstrates complete infrastructure automation from provisioning to deployment using Terraform, Ansible, Jenkins, Docker, and Kubernetes.

📌 Prerequisites
AWS Account

IAM User with Access & Secret Keys

Ubuntu 22.04 machine (control node)

SSH Key Pair (for EC2)

Jenkins, Docker Hub & GitHub Accounts

🧱 Infrastructure Provisioning using Terraform
✅ Install Terraform on Ubuntu

sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update -y
sudo apt-get install terraform -y
terraform --version
✅ Terraform Project Setup
Directory: Terraform-projects-2

File: main.tf

AWS Resources: 1 Master, 3 Worker EC2 Instances

💡 Set access_key, secret_key, ami_id, subnet_id, key_name accordingly.

✅ Terraform Commands

terraform init
terraform validate
terraform plan
terraform apply --auto-approve
⚙️ Configuration Management using Ansible
✅ Install Ansible

sudo apt update -y
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
✅ SSH Setup

ssh-keygen -t rsa
# Copy id_rsa.pub to /home/ubuntu/.ssh/authorized_keys on all EC2 instances
✅ Configure Ansible Inventory

[localhost]
<ansible_node_ip>

[master]
<master_ip>

[worker]
<worker1_ip>
<worker2_ip>
<worker3_ip>
✅ Test Connection
ansible all -m ping
🧾 Ansible Scripts
script1.sh - Install Jenkins & Java

script2.sh - Install Java, Docker, Kubernetes on Master

script3.sh - Install Docker, Kubernetes on Workers

✅ Run Playbook
yaml
---
- name: Jenkins & Java
  hosts: localhost
  become: true
  tasks:
    - script: script1.sh

- name: Master Setup
  hosts: master
  become: true
  tasks:
    - script: script2.sh

- name: Worker Setup
  hosts: worker
  become: true
  tasks:
    - script: script3.sh
🔧 Kubernetes Cluster Setup
✅ On Master Node

sudo kubeadm init --pod-network-cidr=10.244.0.0/16
✅ Join Workers to Cluster
Run the generated kubeadm join command on all workers.

✅ Verify

kubectl get nodes
🧰 Jenkins Setup
✅ Unlock Jenkins

sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Access Jenkins UI: http://<your-ip>:8080

Install suggested plugins

Create admin user

✅ Add Slave Node
Go to Manage Jenkins > Nodes > New Node

Use SSH method to connect master node

Ensure Java is installed and port 22 is open

📦 Docker + GitHub Integration
✅ Fork Project Repository
Fork this: https://github.com/DeepikaPanghal/devops-project.git

✅ Dockerfile Example

FROM ubuntu:latest
RUN apt-get update && apt-get install -y apache2 curl iputils-ping && apt-get clean
ADD . /var/www/html/
EXPOSE 80
ENTRYPOINT ["apachectl", "-D", "FOREGROUND"]
🔁 Jenkins CI/CD Pipeline
✅ Docker Image Build + Push Pipeline

pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('your-credentials-id')
    IMAGE_NAME = "your-dockerhub-username/project2"
  }

  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/<your-username>/devops-project.git'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        sh 'docker push $IMAGE_NAME'
      }
    }
  }
}
☸️ Kubernetes Deployment via Jenkins
✅ Kubernetes Pipeline in Jenkins

pipeline {
  agent none
  environment {
    DOCKERHUB_CREDENTIALS = credentials('your-credentials-id')
  }

  stages {
    stage('Git Clone') {
      agent { label 'Kmaster' }
      steps {
        git 'https://github.com/<your-username>/devops-project.git'
      }
    }

    stage('Docker Build & Push') {
      agent { label 'Kmaster' }
      steps {
        sh '''
          docker build -t your-dockerhub-username/project2 .
          echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
          docker push your-dockerhub-username/project2
        '''
      }
    }

    stage('Deploy to K8s') {
      agent { label 'Kmaster' }
      steps {
        sh '''
          kubectl apply -f deployment.yaml
          kubectl apply -f service.yaml
        '''
      }
    }
  }
}
🧪 Verify Deployment

kubectl get deployments
kubectl get pods
kubectl get svc
Access Application: http://<node-ip>:<node-port>

✅ Outcome
Infrastructure provisioned with Terraform

Configuration with Ansible

CI/CD pipeline set up using Jenkins

Docker image pushed to Docker Hub

Application deployed on Kubernetes cluster

🔗 Resources
Original GitHub Repo: DeepikaPanghal/devops-project

Fork and customize as per your Docker Hub and GitHub credentials
