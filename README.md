#Kops is nothing but kubernetes operations, there are many ways to deploy cluster. 
# create the Management-server
it is nothing specical where there is no need of login to master or worker node, so i am going to manage everythign from my 
Management-server itself.
just create the instance on AWS(t2.medium)

DNS name( i used the GoDaddy, purchase the DNS), once you create the Hosted zones in Route53, then you can see NameServer
so integrate these Nameservers with Godaddy.

S3 Bucket--> to store the state( create S3 bucket in AWS)

Create IAM role assign it to EC2(given admin access), and attach IAM role to EC2
Generate the SSH key, so connect to EC2 and generate the SSH( KOPS will apply key to all the nodes)
Command:  ssh-keygen

Download kops and kubectl to usr/local/bin and change the permissions(chomd 777)
url: kops github( wget and copy the latest releases)

#edit .bashrc and add the all env variables
nano .bashrc
export NAME=syedsinglife.shop
export KOPS_STATE_STORE=s3://syedsinglife.shop
export AWS_REGION=us-east-2
export CLUSTER_NAME=syedsinglife.shop
export EDITOR='/usr/bin/nano'
#export K8S_VERSION=1.6.4
once save the file, than source.bashrc

# create the Cluster using KOPS 
kops create cluster --name=syedsinglife.shop \
--state=s3://syedsinglife.shop --zones=us-east-1a,us-east-1b \
--node-count=2 --control-plane-count=1 --node-size=t3.medium --control-plane-size=t3.medium \
--control-plane-zones=us-east-1a --control-plane-volume-size 10 --node-volume-size 10 \
--ssh-public-key ~/.ssh/id_rsa.pub \
--dns-zone=syedsinglife.shop --dry-run --output yaml

Create the cluster using below command:
kops create -f cluster.yaml

kops update cluster --name syedsinglife.shop --yes --admin
kops validate cluster --wait 10m
kops delete -f cluster.yml  --yes

#Smoke testing
to get node info
command: kubectl get nodes
         kubectl get nodes --no-headers
         kubectl cluster-info
         kubectl get ns
         kubectl get pods
         kubectl get pods -o wide
         kubectl get pods -n kube-system
         kubectl get pods -n kube-system -o wide | grep -I api
         kubectl get pods -n kube-system -o wide | grep -I etcd
         kubectl get pods -n kube-system -o wide | grep -I control
         kubectl get pods -n kube-system -o wide | grep -I scheduler