Pre-requisites
==============

::

	Those pre requisites have already been done in the UDF blueprints. So the only thing you may want to do when deploying a blueprint is to run a apt-get update  && apt-get upgrade -y on each Ubuntu system


to deploy Kubernetes successfully you will need the following: 

* a user that is able to connect to any nodes without prompt and can run sudo command
* docker and bridge-utils should be installed on **each node** (one best practise is to run some binaries in a container instead of as a service: etcd, apiserver, controller manager, and scheduler)
* make sure that the master(s) has/have internet access

.. _source_list:

Setup apt source list
---------------------

To deploy Kubernetes, we will need to add a kubernetes source list to apt to retrieve this package 

on **each master and node**, do the following (**already done in UDF blueprints**):

::

	#this command will identify the version for the distro. For example #xenial ubuntu version
	CODENAME=$(lsb_release -cs)

	curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

	printf "deb http://apt.kubernetes.io/ kubernetes-${CODENAME} main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

	sudo apt-get update


Installation
------------

this section mentions commands that must be run on **all systems**

First thing will be to install docker since some Kubernetes services will run into containers and we will deploy app into containers (for resiliency, availability, etc..). 

::

	sudo apt-get install -y docker.io


once docker is installed, we can install our Kubernetes components: 

::

	sudo apt-get install -y kubelet kubeadm kubectl kubernetes-cni


