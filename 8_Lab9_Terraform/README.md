# Alternate CLI

In the Ensign IT 360 Labs for week 8, students have difficulty running terraform in the Cloud CLI.

This is because when the student changes from one project to the other, their authorization context changes, but their local data drive does not. 

The terraform gets mixed up, fails to create resources, and requires the student to return and do manual cleanup that is a task cut out for an engineer perhaps more skilled than an introductory student.

One way to get around this is to use an alternative toolset like I've built here, aligned to the terraform version 1.5.7 found in Cloud CLI during Spring 2024 semester.

## Build

I built this on an Apple M3, and most of my students are windows so I need to specify a compatible platform:

From Docker folder:

```
 docker buildx build --platform linux/amd64 -t mycli -f Dockerfile .
```

## Run

```
docker run -it --privileged -v /var/run/docker.sock:/var/run/docker.sock mycli /bin/bash
```

## Use
```
gcloud auth application-default login

gcloud auth application-default set-quota-project your_project_name

```

A second login below is meant to be instructional in this case. Students get terraform errors requiring application-default login.
If they try to do this in the browser's cloud CLI they may get an error that the quota project is not set. 

There are tools in the lab that set config values for them, but this gets written to a personal filesystem that is shared across projects. When the project context changes, bad values get written to the filesystem.  

```
gcloud auth login
```
 
## Labs
I've instructed them to do sanity checks on all their values:

```
gcloud config set core/account first.last@ensign.edu

gcloud auth list

gcloud config set core/project your_project_name

gcloud config set compute/region us-central1

gcloud config set compute/zone us-central1-a

gcloud config list

cat ~/gke-security-scenarios-demo/terraform/terraform.tfvars
```

## Lab 9

The container will already contain the copy of code for lab 9. 

```
cd /app/gke-security-scenarios-demo
```

And they can then proceed to task 1. 
The Bastion tends to respond too slow, so the lab has them patch it with sed:

```
sed -i 's/f1-micro/n1-standard-1/g' ~/gke-security-scenarios-demo/terraform/variables.tf
```
```
make setup-project
cat /app/gke-security-scenarios-demo/terraform/variables.tf
```

## Failure Recovery

When the lab fails, it fails hard, and students have to clean up the mess:

1) Remove the Kubernetes cluster for the lab
2) Remove VM instances
3) Remove Subnet
4) Remove VPC
5) Remove Service Account from IAM
6) Remove SSH keys from Compute Engine Settings Metadata

Return and repeat 

Or:

Create a new, clean project and start again, being careful not to switch back and forth between projects in the Browser's CLI Console 


# Changes
I found that changes between projects required Cloud Resource Manager, troubleshooting bastion SSH through IAP required IAP's API to be enabled. I added them to the startup script. 

Also, I had provided instructions that the Bastion did not need to be n1-standard-1 and could stay f1-micro, but I've changed the script in this lab to automatically do the sed that lab9 required originally.

When the container is run as root, terraform builds keys as root but the bastion doesn't allow ssh as root, so now we'll run as cloudsdk
