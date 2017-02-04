Labs setup/access
=================

In this guide, we will cover different setup: 

* 1 basic cluster: 

	* 1 master (no master HA)
	* 2 nodes

* 1 all-in-one deployment

	* 1 server that will be master and node (convenient to run kubernetes on your laptop)


Here is the setup we will leverage to either create a new environment or to connect to an existing environment

==================  ====================  ====================  ============  ====================
     Hostname           Management IP        Kubernetes IP          Role         Login/Password
==================  ====================  ====================  ============  ====================
     Master 1             10.1.1.1            10.1.10.11          Master           root/default
      node 1              10.1.1.2            10.1.10.21           node            root/default
      node 2              10.1.1.3            10.1.10.22           nodes           root/default
      Win7                10.1.1.4            10.1.10.50          Jumpbox            user/user
==================  ====================  ====================  ============  ====================


In case you don't use UDF and an existing blueprint, here are a few things to know that could be useful (if you want to reproduce this in another environment)

Here are the different things to take into accounts during this installation guide: 

* We use *Ubuntu xenial* in the UDF blueprints
* We updated on all the nodes the /etc/hosts file so that each node is reachable via its name

::

	#master host file
	$ cat /etc/hosts
	127.0.0.1       localhost
	10.1.10.11       master1 master1.my-lab
	10.1.10.21       node1  node1.my-lab
	10.1.10.22       node2  node2.my-lab

	#nodes host file
	$ cat /etc/hosts
	127.0.0.1       localhost
	10.1.10.1       master1 master1.my-lab
	10.1.20.21      node1  node1.my-lab
	10.1.20.22      node2  node2.my-lab


* On master1, we created some ssh keys for user that we copied on all the nodes. This way you can use master1 to connect to all nodes without authentication (ssh-keygen, ssh-copy-id)
* we enabled user to do sudo commands without authentication (needed to use ansible with this user). This was done via the visudo command to specify that we allow passwordless sudo command for this user (here is a thread talking about how to do it: `visudo  <http://askubuntu.com/questions/504652/adding-nopasswd-in-etc-sudoers-doesnt-work/504666/>`_)
  

You have many manuals available to explain how to install Kubernetes. If you don't use Ubuntu, you can reference to this page to find the appropriate guide:  `Getting started guides - bare metal  <http://kubernetes.io/docs/getting-started-guides/#bare-metal>`_ 

Here you'll find guides for:

* fedora
* Centos
* Ubuntu
* CoreOS
  
  and some other guides for non bare metal deployment (AWS, Google Compute Engine, Rackspace, ...)


