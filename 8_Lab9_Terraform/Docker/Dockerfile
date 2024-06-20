FROM gcr.io/google.com/cloudsdktool/google-cloud-cli
RUN apt-get update -y && apt-get install vim nano unzip -y && apt clean
RUN curl https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip -o tf.zip
RUN unzip tf.zip
RUN chmod 755 terraform
RUN mv terraform /usr/local/bin
RUN mkdir /app 
RUN chown cloudsdk:cloudsdk /app 
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
RUN chmod 700 get_helm.sh 
RUN ./get_helm.sh 
USER cloudsdk
WORKDIR /app 
RUN git clone https://github.com/GoogleCloudPlatform/gke-security-scenarios-demo
RUN sed -i 's/f1-micro/n1-standard-1/g' /app/gke-security-scenarios-demo/terraform/variables.tf
COPY ./enable-apis.sh /app/gke-security-scenarios-demo/scripts/
WORKDIR /home/cloudsdk
