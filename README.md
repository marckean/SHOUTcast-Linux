# SHOUTcast-Linuxdocker build -t marcregistry.azurecr.io/shoutcast-linux:latest .


## Container Registries

By default the docker tools use the Docker registry which can be found at http://hub.docker.com. However you can use the Azure Container Registry instead. [Create a container registry using the Azure portal](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal).

### Logging in to the Docker Registry
Before we can push an image to the registry we need to login. This can be done using the following command-line


```
docker login -u <username> -p <password> http://<registryname>-on.azurecr.io
```
Change the parameters above to suite your environment.

From this moment on, all commands that interact with the registry are authenticated. If you are using these commands as part of your build scripts it is wise to also use the logout command, so the credentials are never left open on the machine.



## Container Build and Push to Container Registry

```
docker build -t shoutcast-linux:latest .
docker tag shoutcast-linux marcregistry.azurecr.io/shoutcast-linux:latest

docker run -d -p 80:80 -p 81:81 marcregistry.azurecr.io/shoutcast-linux
docker run -d -p 80:80 -p 81:81 --isolation=marcregistry.azurecr.io/nginx-hls

docker push marcregistry.azurecr.io/shoutcast-linux:latest




az group create --name 'SHOUTCAST-Linux' --location australiaeast

az container create --resource-group 'SHOUTCAST-Linux' --name shoutcastlinux --image marcregistry.azurecr.io/shoutcast-linux:latest --registry-password Qd3LUE743AT78tiekt0=Hfwzo3oneTPv --restart-policy OnFailure --ip-address Public --cpu 1 --memory 1 --ports 8000 8001 --location australiaeast --verbose

az container show --resource-group 'SHOUTCAST-Linux' --name shoutcastlinux

az container logs --resource-group 'SHOUTCAST-Linux' --name shoutcastlinux
```
## Run applications in Azure Container Service (AKS)
From here https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-application

### Create Kubernetes cluster
`az aks create --resource-group <ResourceGroupName> --name <AKS-Name> --node-count 2 --generate-ssh-keys --location AustraliaEast`

### Start Kubernetes dashboard
`az aks browse --resource-group <ResourceGroupName> --name <AKS-Name>`

### Connect with kubectl
`az aks get-credentials --resource-group=<ResourceGroupName> --name=<AKS-Name>`

### Configure Kubernetes to use your ACR 
`kubectl create secret docker-registry aks-secret20180105 --docker-server <registryname>.azurecr.io --docker-email=<email> --docker-username=<username> --docker-password <password>`


