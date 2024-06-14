FROM gcr.io/google.com/cloudsdktool/google-cloud-cli
RUN apt-get update -y
RUN apt-get install vim nano -y
#RUN apt install google-cloud-sdk-terraform-tools -y
RUN curl https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip -o tf.zip
RUN apt-get install unzip -y
RUN unzip tf.zip
RUN chmod u+x terraform
RUN mv terraform /usr/local/bin
RUN mkdir /app 
WORKDIR /app 
RUN git clone https://github.com/GoogleCloudPlatform/gke-security-scenarios-demo
WORKDIR /root
