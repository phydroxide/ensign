### Login
Login to GCP with gcloud's CLI

```
gcloud auth login
```
Then paste the authentication challenge in the cli.


### Create GCP Project

I found it is easiest to support students by telling them to create a new project for the assignment, and if things go south, we can nuke it and try again.
```
gcloud projects create ensign-wp --name ensign-wp
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
gcloud container clusters create ensign-wp \
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
gcloud container clusters get-credentials ensign-wp 
```

This will use the Google credentials to get the kubernetes credential and save to ~/.kube/config

Once again, you can verify you're connected and authorized by requesting cluster-info
```
kubectl cluster-info
```

###
