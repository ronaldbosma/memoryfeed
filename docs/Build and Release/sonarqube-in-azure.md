---
tags: [ "Azure", "Azure DevOps", "SonarQube" ]
---

# Host SonarQube in Azure

How to use SonarQube in Azure DevOps using an Azure Container instance to host SonarQube: https://www.azuredevopslabs.com/labs/vstsextend/sonarqube/.

- Start Cloud Shell in Azure Portal with Bash.
- Create Resource Group:
  `az group create --name sonarqube-rg --location westeurope`
- Create Azure Container Instance based on SonarQube Docker image:
  `az container create -g sonarqube-rg --name ronaldbosma-sonarqube-aci --image sonarqube --ports 9000 --dns-name-label ronaldbosma-sonarqube --cpu 2 --memory 3.5`
- SonarQube will be reachable on: http://ronaldbosma-sonarqube.westeurope.azurecontainer.io:9000