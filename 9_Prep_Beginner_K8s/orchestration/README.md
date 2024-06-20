#Kubernetes for Beginners lab

Jenafae asked some questions about the lab that demonstrates Kubernetes deployments.

From the issue that was provided, I believe the solution relates to having to get credentials from gcloud cli for kubectl:

```
gcloud container clusters get-credentials <clustername>
```

In any case I walked through the lab that was referenced to provide some additional insight:

Adapted from https://dev.to/ken_mwaura1/deploying-web-applications-on-kubernetes-a-beginners-guide-4ano

You'll see here code copied from the blog above, with some inline notes on how to take it further.

The lab doesn't go far enough to be able to see the application container in the deployment manifest actually render on the web.

We learned about how to do that last week, and some of that may have been lost in the noise of the bugs that were encountered:

Here is one lab example on where they built a Loadbalancer which served to the Internet:

https://github.com/GoogleCloudPlatform/gke-security-scenarios-demo/blob/master/terraform/modules/instance/manifests/nginx.yaml#L63

Week 7 (7.3) also had some examples
https://github.com/GoogleCloudPlatform/training-data-analyst/tree/master/courses/ak8s/v1.1/Deployments
https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/ak8s/v1.1/Deployments/service-nginx.yaml

Ultimately Kubernetes API Documentation for Deployments, Services, and other resource types is the authoritative source for what you'll need to accomplish your specific use case.

You'll see inline comments in the yaml of this repository that might help.

# Quick run

1) Create cluster
2) Get credentials

```
gcloud congtainer clusters get-credentials <clustername>
```

```
kubectl apply -f cowsay.yaml
```

```
kubectl get deployments
```

```
kubectl apply -f cowsay-service.yaml
```

```
kubectl get services 
``` 

```
kubectl apply -f cowsay-service-ext.yaml
``` 

```
kubectl get services 
``` 

```
kubectl delete service cowsay-service 
``` 
