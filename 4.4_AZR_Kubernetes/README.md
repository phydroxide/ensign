# Exercise 4.4

Create a secure, multi-tenant Azure Kubernetes Service cluster or Multi-zone Azure Container 

## Sample Flask Container

The exercise is to create a cluster. To demonstrate it is working we need a workload to run. 
For a quick win we're going to use a [container image](https://hub.docker.com/r/digitalocean/flask-helloworld) and [sample artifacts](https://github.com/do-community/k8s-intro-meetup-kit) from an older digital ocean meetup kit.

https://hub.docker.com/r/digitalocean/flask-helloworld

https://github.com/do-community/k8s-intro-meetup-kit

```
docker pull digitalocean/flask-helloworld
```

Then we prove it can run locally:
```
docker run -p 8000:5000 digitalocean/flask-helloworld
```

Visit http://localhost:8000


The kubernetes orchestrations for this project can be cloned from the repository
```
git clone git@github.com:do-community/k8s-intro-meetup-kit.git
```

## Customize helloworld container

I don't need to do this but sometimes I want to add tools inside the demo container so I can play with it.
It's also an example of how you might stack your own customizations onto a container image someone already provided.

```
cd Docker_flask
docker build -f ./Dockerfile -t helloflask:ensign . 
``` 



## Build containerized cli tools

I don't want to have to install a bunch of tools on my machine, and if I switch machines, I want a platform I can re-use. So I'm going to create a docker container with Azure's CLI and kubectl installed. 

```
cd Docker_kubectl
docker build -f ./Dockerfile -t kubectl:ensign . 
```

I built it with docker's cli inside, and if I want to run an interactive shell inside the container, I mount the meetup kit and docker socket into the container. 
```
docker run --name cli_tools --privileged -it -v k8s-intro-meetup-kit:/k8s -v /var/run/docker.sock:/var/run/docker.sock kubectl:ensign /bin/bash
```


## Push image to container registry

After creating a container registry in Azure I can push my demo flask image to it.
https://learn.microsoft.com/en-us/azure/container-registry/

```
root@cli_tools#
az login
```

This will prompt me to visit https://microsoft.com/devicelogin  and enter a code. My CLI is now authenticated.

Then I login to the registry:
```
az acr login --name myregistry
docker tag digitalocean/flask-helloworld ensignprod.azurecr.io/samples/helloflask:ensign
docker push ensignprod.azurecr.io/samples/helloflask:ensign
```

## The demo already has it

This is all to demonstrate the pieces you have to pull together to run an application. 

In reality k8s meetup kit orchestration yaml's reference digitalocean's own repository, for example [flask-deployment.yaml line 19](https://github.com/do-community/k8s-intro-meetup-kit/blob/master/k8s/flask-deployment.yaml#L19)

```
image: digitalocean/flask-helloworld:latest

```

To use our own we might replace the container URL with our own:
```
image: ensignprod.azurecr.io/samples/helloflask:ensign 

```
