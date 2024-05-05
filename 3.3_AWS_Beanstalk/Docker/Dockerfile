FROM python:3.11-bullseye
RUN apt-get update
RUN apt-get install python3-pip -y
RUN python3 -m pip install virtualenv 
RUN mkdir /eb-flask
RUN cd /eb-flask
RUN virtualenv virt
RUN pip install flask==2.0.3
RUN pip freeze
RUN pip freeze > /eb-flask/requirements.txt
COPY application.py /eb-flask/application.py
