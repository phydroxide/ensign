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
RUN sed -i 's/f1-micro/n1-standard-1/g' /app/gke-security-scenarios-demo/terraform/variables.tf
COPY ./enable-apis.sh /app/gke-security-scenarios-demo/scripts/
WORKDIR /root
