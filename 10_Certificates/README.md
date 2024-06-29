# Certificates

## Start CLI environment
From my Docker toolkit for students on Windows

```
docker run -it --privileged --name ensignwindows  -p 8080:8080 us-central1-docker.pkg.dev/ensign-421602/ensign-public/ensign-cli:windows /bin/bash
```

## Prepare GCP Project Environment 
Login to GCP
```
gcloud auth login
```


I start with a clean project
```
gcloud projects create encert
gcloud config set project ensigncert
gcloud billing projects link ensigncert --billing-account=0101F3-XXXXX-ZZZZZ
```

Enable the Kubernetes API

```
gcloud services enable container.googleapis.com
```

## Create Cluster

Creating an Autopilot Cluster makes me have to think less, but seems to take more time to provision. Autopilot clusters must be regional. 
```
gcloud container clusters create-auto autopilot-cluster-1 --region us-central1
```

It takes over 10 minutes for an autopilot cluster to finish provisioning. 


## Now What?
A lot of students struggled with what to do next. 
The reading for 10.1 addresses how to [Use Google Managed Certificates](https://cloud.google.com/kubernetes-engine/docs/how-to/managed-certs?hl=en&_ga=2.42614240.-437533536.1661485432)
It says the HttpLoadBalancing add-on must be enabled. 

Remember that Kubernetes is a technology leveraged by many Clouds, so there are many ways to accomplish this. Google's way directs you to enable the add-on, which is available through their gcloud cli.

So I do a quick google search on "HttpLoadBalancing add-on kubernetes." Maybe I also add "GKE" to constrain the search results. Turns out I could have found this in their api documentation as well. 

[Configure Ingress(https://cloud.google.com/kubernetes-engine/docs/how-to/load-balance-ingress) for external Application Load Balancers.



## Patch Cluster with HttpLoadBalancing

This takes some time. You can also specify it upon creation.
```
gcloud container clusters update autopilot-cluster-1 --update-addons=HttpLoadBalancing=ENABLED --region us-central1
```

Google tends to do a stellar job with their documentation. There are samples I copy and paste into my repository (see the files included in this repository's 'orchestration' directory). 

## Reserve Static IP

Both of the references above state I need to configure a [Static IP](https://cloud.google.com/kubernetes-engine/docs/concepts/ingress-xlb#static_ip_addresses_for_https_load_balancers)

```
gcloud compute addresses create weather-ip --global --ip-version IPV4
```

## Apply Orchestrations

```
git clone https://github.com/phydroxide/ensign.git
cd ensign
git checkout Certificates
cd orchestrations
```

Connect to the cluster that's been patched with HttpLoadBalancing

```
gcloud container clusters get-credentials autopilot-cluster-1 --zone=us-central1 
```

## Application Load Balancer
I also need an [External Applcation Load Balancer](https://console.cloud.google.com/welcome?walkthrough_id=load-balancing--ext-https-load-balancer-ingress&_ga=2.244656581.1162373025.1719459190-2005931062.1714184856)
