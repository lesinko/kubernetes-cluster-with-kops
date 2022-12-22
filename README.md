# setup kubernetes cluster using kops

## Pre-requisites
-Aws account with aws CLI configured
-kubectl installed
-jq manipulation tool
-Active dedicated "kops" subdomain

## install AWSCLI
curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
apt install unzip python
 unzip awscli-bundle.zip
 #sudo apt-get install unzip - if you dont have unzip in your system
 ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws

## Install kubectl
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

## create "kops" IAM user with the required permissions.
-AmazonEC2FullAccess
-AmazonRoute53FullAccess
-AmazonS3FullAccess
-IAMFullAccess

## Configure Route53 DNS setup
-Create a hosted zone in aws for your subdomain
-Add NS records in the DNS setting of your main domain
-Verify NS records using the dig tool

## create an S3 bucket
 aws s3 mb s3:// bucket-name

## Install kops on ubuntu/amazon ec2 instance:
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops

## Expose environment variable- configure the below commands in your editor: 
use nano ~/. bashrc
export KOPS_CLUSTER_NAME= cluster-name (subdomain name)
export KOPS_STATE_STORE=s3://bucket-name

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
  kops update cluster --name (cluster-name) --yes

  ## verify your cluster
  kops get cluster

## Validate your cluster health
 kops validate cluster --wait 10min

## Login to master Node from (ec2 instance)
Ssh admin@api(cluster-name)

## To list nodes
  kubectl get nodes 

## modify kubernetes cluster
kops get instance groups - list nodes
kops edit instancegroups (node-name) -edit configurations

## Delete cluster and all resources
kops delete cluster --name (cluster-name) --yes

