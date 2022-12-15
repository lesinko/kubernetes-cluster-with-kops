# setup-k8s-cluster
## Launch an EC2 instance
## install AWSCLI
curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
apt install unzip python
 unzip awscli-bundle.zip
## Install kubectl
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
## Create an IAM role with Route53, EC2, IAM and S3 full access
## Attach IAM role to ubuntu server
## Install kops on ubuntu instance:
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
## Create a Route53 private hosted zone (you can create Public hosted zone if you have a domain)
## create an S3 bucket
 aws s3 mb s3://k8s-bucket2
## Expose environment variable- configure the below commands in your editor: 
use nano ~/. bashrc
export KOPS_CLUSTER_NAME=cluster name
export KOPS_STATE_STORE=s3://bucket name

## Reflect variables added to. bashrc using:
Source ~/. Bashrc
## Create ssh keys before creating cluster -Enables key based for authorization
 ssh-keygen
## Create kubernetes cluster definitions on S3 bucket
 kops create cluster \
--state=${KOPS_STATE_STORE} \
--node-count=2 \
--master-size=t2.micro \
--node-size=t2.micro \
--zones=us-east-1a \
--name=${KOPS_CLUSTER_NAME} \
--dns private \
--master-count 1

## Create kubernetes cluster
  kops update cluster --yes
## Validate your cluster
 kops validate cluster
## Login to master Node
Ssh admin@domain name
## To list nodes
  kubectl get nodes 
## Delete cluster
kops delete cluster --yes
