## Prompt

Problem: 
	Based on your week one assignment and recommendations, your CEO has approved migrating your application infrastructure to google cloud to host your VMs, and other services, websites, and business applications.  

Solution: 
	  Create a free google cloud account using this link: https://cloud.google.com/freeLinks to an external site.
	  Use the free credits to create one Linux/Ubuntu VM with minimum specifications.  
	  Deploy a word press website with content from your week one proposal. 
	  You may add images to your word press site if needed. 
	  Include screenshots of your VMs and links to your word press website as part of your assignment

## GCloud CLI Platform

```
docker run --privileged  \
           -u root  \
           --name cli \
           -v cli:/home/cloudsdk \
           -v /var/run/docker.sock:/var/run/docker.sock \
           -it  gcr.io/google.com/cloudsdktool/google-cloud-cli:latest 
```


## Build container
```
docker build -t wordpress:ensign -f Dockerfile .
```

```
cli$
gcloud auth login 
gcloud config set project ensign
gcloud auth configure-docker us-central1-docker.pkg.dev

docker tag wordpress:ensign us-central1-docker.pkg.dev/ensign/repo/wordpress:ensign
docker push wordpress:ensign us-central1-docker.pkg.dev/ensign/repo/wordpress:ensign

```

