#FROM ubuntu:24.04
FROM debian:bookworm 
RUN apt-get update && apt-get install apt-transport-https ca-certificates curl gnupg lsb-release -y
RUN mkdir -p /etc/apt/keyrings
RUN curl -sLS https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o /etc/apt/keyrings/microsoft.gpg 
RUN chmod go+r /etc/apt/keyrings/microsoft.gpg 
RUN mkdir /app
COPY k_install.sh /app 
RUN chmod u+x /app/k_install.sh
RUN /app/k_install.sh 
RUN apt-get update && apt-get install azure-cli -y
RUN apt-get update && apt-get install docker.io -y 
