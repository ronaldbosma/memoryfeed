How to run SonarQube in Azure DevOps using an Azure Container instance: https://www.azuredevopslabs.com/labs/vstsextend/sonarqube/.


- Start Cloud Shell in Azure Portal with Bash.
- Create Resource Group:
  `az group create --name sonarqube-rg --location westeurope`
- Create Azure Container Instance based on SonarQube Docker image:
  `az container create -g sonarqube-rg --name ronaldbosma-sonarqube-aci --image sonarqube --ports 9000 --dns-name-label ronaldbosma-sonarqube --cpu 2 --memory 3.5`
- SonarQube will be reachable on: http://ronaldbosma-sonarqube.westeurope.azurecontainer.io:9000

  Result:
  
	{
	  "containers": [
		{
		  "command": null,
		  "environmentVariables": [],
		  "image": "sonarqube",
		  "instanceView": {
			"currentState": {
			  "detailStatus": "",
			  "exitCode": null,
			  "finishTime": null,
			  "startTime": "2019-08-11T10:47:25+00:00",
			  "state": "Running"
			},
			"events": [
			  {
				"count": 1,
				"firstTimestamp": "2019-08-11T10:46:43+00:00",
				"lastTimestamp": "2019-08-11T10:46:43+00:00",
				"message": "pulling image \"sonarqube\"",
				"name": "Pulling",
				"type": "Normal"
			  },
			  {
				"count": 1,
				"firstTimestamp": "2019-08-11T10:47:21+00:00",
				"lastTimestamp": "2019-08-11T10:47:21+00:00",
				"message": "Successfully pulled image \"sonarqube\"",
				"name": "Pulled",
				"type": "Normal"
			  },
			  {
				"count": 1,
				"firstTimestamp": "2019-08-11T10:47:25+00:00",
				"lastTimestamp": "2019-08-11T10:47:25+00:00",
				"message": "Created container",
				"name": "Created",
				"type": "Normal"
			  },
			  {
				"count": 1,
				"firstTimestamp": "2019-08-11T10:47:25+00:00",
				"lastTimestamp": "2019-08-11T10:47:25+00:00",
				"message": "Started container",
				"name": "Started",
				"type": "Normal"
			  }
			],
			"previousState": null,
			"restartCount": 0
		  },
		  "livenessProbe": null,
		  "name": "ronaldbosma-sonarqube-aci",
		  "ports": [
			{
			  "port": 9000,
			  "protocol": "TCP"
			}
		  ],
		  "readinessProbe": null,
		  "resources": {
			"limits": null,
			"requests": {
			  "cpu": 2.0,
			  "gpu": null,
			  "memoryInGb": 3.5
			}
		  },
		  "volumeMounts": null
		}
	  ],
	  "diagnostics": null,
	  "dnsConfig": null,
	  "id": "/subscriptions/d57464b3-d2fa-4386-8f2c-0c2720676b44/resourceGroups/sonarqube-rg/providers/Microsoft.ContainerInstance/containerGroups/ronaldbosma-sonarqube-aci",
	  "identity": null,
	  "imageRegistryCredentials": null,
	  "instanceView": {
		"events": [],
		"state": "Running"
	  },
	  "ipAddress": {
		"dnsNameLabel": "ronaldbosma-sonarqube",
		"fqdn": "ronaldbosma-sonarqube.westeurope.azurecontainer.io",
		"ip": "51.105.185.169",
		"ports": [
		  {
			"port": 9000,
			"protocol": "TCP"
		  }
		],
		"type": "Public"
	  },
	  "location": "westeurope",
	  "name": "ronaldbosma-sonarqube-aci",
	  "networkProfile": null,
	  "osType": "Linux",
	  "provisioningState": "Succeeded",
	  "resourceGroup": "sonarqube-rg",
	  "restartPolicy": "Always",
	  "tags": {},
	  "type": "Microsoft.ContainerInstance/containerGroups",
	  "volumes": null
	}