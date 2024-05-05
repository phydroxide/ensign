# Beanstalk

Follow instructions to build a [Beanstalk Cluster](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.html)

[Create Beanstalk Environment](https://us-east-2.console.aws.amazon.com/elasticbeanstalk/home?region=us-east-2#/create-environment)

[Deploy a Sample App](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.DeployApp.html)


[Deploy a Custom App](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-flask.html)

## Deploy a Custom App

```
docker pull python:3.11
```

```
docker pull python:3.11-bullseye
```

## Run Flask Container

I don't want to have to install XCode and then Python on my Mac, So I downloaded docker desktop and run a container to do my development. I'm going to develop in a path eb-flask in my current directory.

```
docker run -v eb-flask:/eb-flask -it --rm python:3.11 /bin/bash
```

Inside that docker container I need some software as prerequisites for the AWS instructions. 


```
apt-get update
apt-get install python3-pip
python3 -m pip install virtualenv
cd /eb-flask
```

Then follow the instructions from [AWS](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-flask.html)

```
mkdir eb-flask
cd eb-flask
virtualenv virt
source virt/bin/activate
pip install flask=2.0.3
pip freeze
pip freeze > requirements.txt
```

## Using Dockerfile

The Dockerfile automates some of this for me. 


```
docker build -f Docker/Dockerfile -t python:ensign . 
```

I can't use port 5000, as it seems MacOS uses that for ControlCenter AirPlay

```
sudo lsof -i -P | grep LISTEN | grep :5000
```

```
docker run -v eb-flask:/eb-flask -p 5001:5000 -it python:ensign /bin/bash
```

```
python3 /eb-flask/application.py
```

## Notes

I upgraded to the newest version of Flask (3.0.3) because the other was failing. 

## TODO

Something I did broke the network plumbing between Docker on the Host and docker containers. 

I just zipped up application.py and requirements.txt and pushed to my Elastic Beanstalk deployment. Port 8001 doesn't work there, so I reverted to 5000 
