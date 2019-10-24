# Setup Kubernetes cluster using vagrant.



## Pre-requisites for Windows64 bit setup.

 * ** Download & Install [Vagrant 2.1.4+](https://www.vagrantup.com)**
 * ** Download & Install Install [Virtualbox 5.2.18+](https://www.virtualbox.org)**
 
  ## Step 1: Install Vagrant  and Setup vagrant Path in Enviorment variable and restart the machine.
  
  
  ## Step 2:clone or download this repo from github.
  * git clone https://github.com/Nishantbarh/kubernates.git.
  
  
  
  ## Step 3:Execute the following vagrant command to start a new Kubernetes cluster, this will start one master and two nodes:
 
```
cd into cloned directory and run the below command
vagrant up
```

You can also start invidual machines by vagrant up k8s-master, vagrant up k8s-node1 and vagrant up k8s-node2

If more than two nodes are required, you can edit the servers array in the Vagrantfile

```
servers = [
    {
        :name => "k8s-node-3",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20180831.0.0",
        :eth1 => "192.168.205.13",
        :mem => "2048",
        :cpu => "2"
    }
]
 ```

As you can see above, you can also configure IP address, memory and CPU in the servers array. 
## Step 4:Once cluster is up  then login into master machine as:
```
vagrant ssh k8s-master
```
## Step 5:Run some utility like kubernate dashbaord  in cluster:
```
cd /vagrant/utility/

kubectl apply -f calico.yaml
kubectl apply -f rbac-kdd.yaml
kubectl apply -f kubernetes-dashboard.yaml
kubectl apply -f admin-user.yaml
kubectl apply -f heapster.yaml
kubectl apply -f influxdb.yaml
```
## Step 5:verify the cluster status as 
```
kubectl get nodes
```
## setup Ngnix loadbalncer.
```
cd  /vagrant/ingress/
kubectl apply -f ingress-nginx.yaml
```

verify the installtion .

Kubectl get pods -n ingress-nginx

## setup Monitoring using promotheus and Grafana.
```
cd /vagrant/monitoring/
go in every folder and run all the yaml.

```
some installation link is provided as:

https://medium.com/faun/production-grade-kubernetes-monitoring-using-prometheus-78144b835b60
## Setup Application using Nodeport.

step 1:create one docker hub  command
https://hub.docker.com/  with your own username and password and email.

step 2 :after login create your own repository.

clone  sample apps from 
git clone https://github.com/Nishantbarh/apps

step 3:cd into  cloned directory 

modify the docker-compose yaml file as per your own docker repo path(username/reponame)

then run command as

docker-compose up

this command will build all the images locally.
then login into docker hub from your master machine as
docker login (provide your own  docker hub username and passowrd)

then run the command as 

docker-compose push

it will push the images into your docker hub repo.

To link your docker hub credentails  in kubernate  cluster please execute the command to create the secret.
```
kubectl create secret docker-registry mycred --docker-server=https://index.docker.io/v1/  --docker-username=XXXX --docker-password=XXX --docker-email=xxxx@gmail.com
```
now refer the mycred secret created for pulling the images form docker hub.

for demostration i have build three application you can build your own.
Now you can deploy all the application from k8-deployment scripts folder from each of the application


## Create ingress or Load Balancer Rule.
The sample Ingress rule has been created and it's avaible into Document-service/k8-deployment scripts

create your own ingress and run as kubectl apply -f myingress.yaml.

verify all the installtion using ingress port.

all the app port can be known running the below command.
```
Kubectl get svc -n kube-system
Kubectl get svc -n default
Kubectl get svc -n monitoring
Kubectl get svc -n ingress-nginx
```


## Stoping the cluster.
```
vagrant halt
```

## Clean-up

Execute the following command to remove the virtual machines created for the Kubernetes cluster.
```
vagrant destroy -f
```

You can destroy individual machines by vagrant destroy k8s-node-1 -f


