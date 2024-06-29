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
cd 10_Certificates 
cd orchestration
```
When you're done you'll have three three services pointing to my weather app and two demo apps, and an Ingress that can be configured to a Google Backend

<img width="641" alt="image" src="https://github.com/phydroxide/ensign/assets/31145228/66b1d894-7584-4426-91d8-7ccf2a4fcb7f">


Connect to the cluster that's been patched with HttpLoadBalancing

```
gcloud container clusters get-credentials autopilot-cluster-1 --zone=us-central1 
```

## Application Load Balancer
I also need an [External Applcation Load Balancer](https://console.cloud.google.com/welcome?walkthrough_id=load-balancing--ext-https-load-balancer-ingress&_ga=2.244656581.1162373025.1719459190-2005931062.1714184856)

Part of this process includes:
* Creating a Front End
* Registering the IP in DNS
* Connecting the Backend IPs, Ports, and Path Routes
* Creating a Certificate
* Validating the Certificate

You can try the Backends that are created by Kubernetes Services, or point to the Ingress which is what I did. 

## Certificates

Load Balancers allow the creation of a certificate. You can Generate the CSR yourself, have an authority issue you a cert and import it, or let Google Manage it for you.

You'll need to create a DNS record to validate the certificate challenge to prove you own the domain. 
![image](https://github.com/phydroxide/ensign/assets/31145228/ac843d64-895f-4518-a26b-48423c0707a5)

It will ask you to create DNS authorization

![image](https://github.com/phydroxide/ensign/assets/31145228/9e479fec-9b8f-4388-b06d-58050ac08680)


![image](https://github.com/phydroxide/ensign/assets/31145228/3a4a8fa5-7731-4abb-9c18-0f08d49ce130)


You'll set up the appropriate DNS record in your project where you registered your domain
![image](https://github.com/phydroxide/ensign/assets/31145228/887b7367-a297-48a7-8983-22bea4b0a893)

## Victory

If your site allows a visit at https://my.domainname.com then it's working!

![image](https://github.com/phydroxide/ensign/assets/31145228/adda334b-af5d-4427-941a-cf6e8c9c18ef)

[Secure Weather](https://myweather.phydroxide.com/server)
[Alt Path 1](https://myweather.phydroxide.com/v1)
[Alt Path 2](https://myweather.phydroxide.com/v2)

![image](https://github.com/phydroxide/ensign/assets/31145228/1c8c5bec-3cb8-4477-910a-d021cc615f31)


## References
[More Ingress Annotations](https://github.com/kelseyhightower/ingress-with-static-ip/blob/master/README.md)
