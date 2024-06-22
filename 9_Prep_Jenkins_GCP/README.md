# Jenkins on GCP

Based on https://www.cloudskillsboost.google/focuses/1776?parent=catalog




## Custom CLI Tools

For BYUI/Ensign Pathways IT 360 I created a docker container that could be used on a student's computer rather than having to rely on Cloud Run or any requirement to install a bunch of tools:

[Alternate CLI Container Image](https://github.com/phydroxide/ensign/tree/main/8_Lab9_Terraform#alternate-cli) 

Note the backslashes have no spaces after them, they represent that the line wraps to the next one, as if they were on the same line.

```
docker run -it --privileged \
  -v /Users/phydroxide/cloudsdk:/home/cloudsdk \
  -v /var/run/docker.sock:/var/run/docker.sock \
  us-central1-docker.pkg.dev/ensign-421602/ensign-public/ensign-cli:smaller \
  /bin/bash
```

### Parameters explained
`docker` is the command-line binary you're able to run by having installed Docker Desktop.

`run` is the command you're asking to perform. Other commonly used commands are `docker build`, `docker pull`, `docker container ls`, etc.

`-it` gives an interactive "tty" which lets you run commands like you were on the command line.

`--privileged` this lets docker run on your workstation/laptop with extra privileges, giving access for example to the docker.sock socket.

`-v` you're mounting a volume or file from your workstation into the container so it can access OS files, code, etc.

/var/run/docker.sock on the left side of the ':' is the actual file on your filesystem

/var/run/docker.sock on the right side of the ':' is where you've mounted docker's socket within the container so you can run docker within
us-central1-docker.pkg.dev: This is GCP's artifact repository that I've enabled in my project in the us-central region.

You could also add here `-v Some\Local\Path\:/root` so you have access to the development files in your home directory. 

/ensign-421602/: GCP's artifact repository is multi-tenant. This is my project name so you can reach my container registry from that URL.

/ensign-public/: This is the name I've given to my repository. I also granted the 'allUsers' principal access as Artifact Registry Reader.
Because of the permissions I've set you do not have to run gcloud auth configure-docker to reach my container image, just run docker pull/run.

/ensign-cli is the name I've given to my container. I built it with `docker build -t localname`, tagged it as ensign-cli at the url above, then pushed.

:smaller is the tag I've given to this revision, indicating the work i went through to make it less bulky

/bin/bash is the command that the docker container runs. This is the Bourne Again SHell for linux. 
You could also choose '/bin/sh', or some other shell. Docker containers typically though run some service like nginx or flask.  

#### Parameter Position and Misc

The /bin/bash command comes last, as it will be run within the container, immediately prior to that should be the image name. Other parameters come after "docker run" and before the image name "us-central...../ensign-cli:smaller". Or you'll get errors that will confuse you.

You could also add `--rm` so it removes the container instance when you're done, otherwise they hang around. Check it out with docker `container ls -a`.

The container name is usually a unique randomization of characters unless you also pass the `--name` parameter to docker. 

The container name shows up inside the container shell as the hostname such as in the prompt below:
 
`root@4b9fc1f0c8d2`

If you want to clean these up you have to stop them, and remove them:

`docker rm 4b9f` works so long as there are no other containers that begin with '4b9f'

If you'd rather reuse them, you start them and reattach to them.

`docker container ls -a`

`docker start 4b9f` 

`docker attach 4b9f` 

"exit" or Ctrl+D will get you out. 


## Runnning Pre-Built gcloud-cli tools

During week 9 I built and hosted a docker image to assist students in having the same tools as I.

I've configured my artifact registry such that allAuthenticatedUsers and allUsers have Artifact Registry Read access.

```
docker pull us-central1-docker.pkg.dev/ensign-421602/ensign-public/ensign-cli:smaller
```

```
us-central1-docker.pkg.dev/ensign-421602/ensign-public/ensign-cli:smaller
```

Run:
```
docker run --rm --name ensign-cli -it --privileged  -v /var/run/docker.sock:/var/run/docker.sock us-central1-docker.pkg.dev/ensign-421602/ensign-public/ensign-cli:smaller /bin/bash
```

You're now using the same tools as me - which include:

gcloud

kubectl

helm

nano

docker
```


## GCP Setup for [Jenkins Lab](https://www.cloudskillsboost.google/focuses/1776?parent=catalog)

First we need to do an initial setup of the GCP Project:

### Login
Login to GCP with gcloud's CLI

```
gcloud auth login
```
Then paste the authentication challenge in the cli. 


### Create GCP Project

I found it is easiest to support students by telling them to create a new project for the assignment, and if things go south, we can nuke it and try again.
```
gcloud projects create ensign-jenkins --name ensign-jenkins 
```

They'll need to enable the compute API. 
```
gcloud services enable compute.googleapis.com
```

Now you can set the compute region and zone
```
gcloud config set compute/region us-central1
```

```
gcloud config set compute/zone us-central1-a
```

### Create Kubernetes Cluster

First enable the container API 
```
gcloud services enable container.googleapis.com
```

Now Create the cluster
```
gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--scopes "https://www.googleapis.com/auth/projecthosting,cloud-platform"
```

Verify Cluster is built:
```
gcloud container clusters list
```

gcloud's cli will automatically create the cluster and put credentials in ~/.kube/config. 
If the session expires, or you need to login from elsewhere to manage the cluster you'll need to generate credentials:

```
gcloud container clusters get-credentials jenkins-cd
```

This will use the Google credentials to get the kubernetes credential and save to ~/.kube/config

Once again, you can verify you're connected and authorized by requesting cluster-info
```
kubectl cluster-info
```

### 

