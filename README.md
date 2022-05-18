# 3 Nodes Kubernetes-Cluster Using Vagrant 
We're going to use the kubeadm tool to bootstrap a K8s cluster with a Master node, and two worker nodes on Ubutu 20.04. We will follow K8s best practices as outline in the [kubernetes documentation](https://kubernetes.io)

## Pre-requisites
```
Vagrant 2.25 or latest
Oracle VM Virtualbox
Master Node - 2 cpus, 2 GB Memory Min
Worker Nodes - 2 cpus, 2 GB Memory Min
```
We will use the vagrantfile below, to automate the provisioning of 1 Master and 2 worker nodes with an IP network range of `192.168.56`

![masternode](https://user-images.githubusercontent.com/100249382/168925941-8e08f8e8-46b8-4f24-b5ca-2d6d74599c2a.JPG)
![workernodes](https://user-images.githubusercontent.com/100249382/168926051-d83880eb-1a60-4bad-b21a-3f3b16a2dbd9.JPG)
To provision the VMS, run the command `vagrant up` and once they are successfully created, run the `vagrant status` command to confirm
![2](https://user-images.githubusercontent.com/100249382/168926839-dd3a3e3e-997a-4e87-9297-1c58ba5a9c15.JPG)
To access one of these systems, run the `vagrant ssh` command

![vagrant ssh kubemaster](https://user-images.githubusercontent.com/100249382/168928057-735c4004-daf2-4217-8784-8bebc2a7aeb7.JPG)


## 2- INSTALL CONTAINER RUNTIME ON THE NODES.

We will be using docker as the Container runtime

![update to install docker](https://user-images.githubusercontent.com/100249382/168928542-35f29809-d04a-4a08-8240-ee02ca8e9f05.JPG)
![install docker](https://user-images.githubusercontent.com/100249382/168928567-c0e522b4-b8f3-41a4-9e6b-4253ac53a9fb.JPG)

Start and Enable docker 

![start and enable docker](https://user-images.githubusercontent.com/100249382/168928652-1a93ae03-ede2-4d32-afe4-30b962ce950b.JPG)

## 3- Disable the firewall and turnoff the “swapping”

![disable firewalland swap](https://user-images.githubusercontent.com/100249382/168928798-b5f1e1c9-ade0-4d27-94fa-4c6ed0715903.JPG)



## 4- Install “apt-transport-https” package

To download the kubernetes, we need to install “apt-transport-https” package on both master and worker nodes

![https transport](https://user-images.githubusercontent.com/100249382/168929040-bcecb473-ec43-489c-8dbe-5a7ca11acf37.JPG)


## 5- Download the public keys

We need public keys to have access to Google Cloud and download packages
So we run the following command on both Master and Worker Nodes to get the public keys

![google public keys](https://user-images.githubusercontent.com/100249382/168929322-7a740374-0df8-4125-8f4e-d7c669e79d4d.JPG)

## 6- Add kubernetes apt repository

Add this repo on all our Master and Worker Nodes

![add k8s repo](https://user-images.githubusercontent.com/100249382/168929649-1b632561-3d27-4964-ab55-c335c601696e.JPG)


## 7- INSTALL KUBERNETES

With the K8S repo now available, it's time to install Kubernetes on all our Nodes by running the following commands

![install k8s](https://user-images.githubusercontent.com/100249382/168929776-5228d4dd-5a99-46b7-9c80-466b9b755358.JPG)


## 8- ENABLE AND START KUBELET

Enable and start the kubelet service on all three Nodes

![enable and start kubelet](https://user-images.githubusercontent.com/100249382/168930021-cfdc91a9-02f6-4de8-9b43-cf2dedb2fd26.JPG)

To see the current version installed we can run `kubectl version --short`


![k8s version](https://user-images.githubusercontent.com/100249382/168930203-8fd54f68-0a22-4988-9d99-d89b8f9b1aaa.JPG)



## 9- Initialize the kubernetes cluster

We now initialize the cluster by running the `kubeadm init` command on the Master Node

![kubeadm init](https://user-images.githubusercontent.com/100249382/168930346-ee044838-7221-4883-9207-325b24ce7218.JPG)

Once the initialization is completed, we will get further instructions, and a token, that our Worker Nodes can use to join the cluster


![joining info](https://user-images.githubusercontent.com/100249382/168931097-f36c1cb4-6d86-4481-9f47-1e2b800de779.JPG)


## 10- Move kube config file to current user Only on the Master Node

To interact with the kubernetes cluster and to use the `kubectl command`, we need to have the kube config file in the home path.

Use the following command to get the kube config file and put it under working directory.

![create home dir](https://user-images.githubusercontent.com/100249382/168931416-95becf2b-c545-4779-b98f-41561c3ff4d2.JPG)


## 11- Install a Pod Network Addon (CNI)

This Container Network Interface (CNI) is installed on the Master Node to enable networkcommunication between containers in the cluster. There are lots of options so we go with Weave-Net

![install weave net](https://user-images.githubusercontent.com/100249382/168931711-5dc54d4f-0590-47d4-92ba-e3c83931f39b.JPG)

## 12- Invite Worker Nodes to Join The Cluster

Using the token generated from the kubeadm join command, the two Worker Nodes can now join the cluster

![node01 join cluster](https://user-images.githubusercontent.com/100249382/168932069-3841e19c-72fc-4e33-b180-1e661ad2e3db.JPG)

To verify if the Nodes joined successfully, we run the `kubectl get nodes` command on the Master Node

![nodes ready](https://user-images.githubusercontent.com/100249382/168932229-ca1851ca-6281-44ac-9df9-b6ad29a0fddd.JPG)

## 13- Check Control-Plane Objects

![controlplane objects](https://user-images.githubusercontent.com/100249382/168932407-4af1d39b-5d6f-47a0-a563-a0c8bb97e2e2.JPG)


With this, we have successfully used the kubeadm tool to bootstrap a Kubernetes cluster

