Pre-requisites
==============

::

	Those pre requisites have already been done in the UDF blueprints. So the only thing you may want to do when deploying a blueprint is to run a apt-get update  && apt-get upgrade -y on each Ubuntu systems


to deploy Kubernetes successfully you will need the following: 

* a user that is able to connect to any nodes without prompt and can run sudo command
* docker and bridge-utils should be installed on **each node** (one best practise is to run some binaries in a container instead of as a service: etcd, apiserver, controller manager, and scheduler)
* make sure that the master(s) has/have internet access

Install and setup docker
------------------------
We have to install docker-engine on the masters and nodes to be able to run docker containers

on **each master and node**, do the following:

::
	#this command identify the distro: ie ubuntu (a line starting with # is a comment, don't execute)
	DISTRO=$(lsb_release -is | tr '[:upper:]' '[:lower:]') 

	#this command will identify the version for the distro. For example #xenial  ubuntu version)
	CODENAME=$(lsb_release -cs)

	sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

	printf "deb https://apt.dockerproject.org/repo ${DISTRO}-${CODENAME} main" | sudo tee /etc/apt/sources.list.d/docker.list

	sudo apt-get update

	sudo apt-get install -y docker-engine bridge-utils


