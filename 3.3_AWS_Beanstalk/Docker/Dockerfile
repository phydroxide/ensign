FROM python:3.11-bullseye
RUN apt-get update
RUN apt-get install python3-pip -y
RUN python3 -m pip install virtualenv 
RUN adduser python
RUN usermod -aG python python 
RUN mkdir /eb-flask
RUN chown python.python /eb-flask
RUN cd /eb-flask
#RUN virtualenv virt
RUN pip install flask==3.0.3
RUN pip freeze
RUN pip freeze > /eb-flask/requirements.txt
RUN python3 -m pip install -r /eb-flask/requirements.txt 
COPY ./eb-flask/application.py /eb-flask/application.py
COPY ./startup.sh /
USER python
EXPOSE 5000 
ENTRYPOINT ["/startup.sh"]
