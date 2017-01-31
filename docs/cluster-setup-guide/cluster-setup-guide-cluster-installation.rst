Cluster installation
====================

Overview
--------

As a reminder, in this example, this is our cluster setup: 

==================  ====================  ====================  ============
     Hostname           Management IP        Kubernetes IP          Role
==================  ====================  ====================  ============
     Master 1             10.1.1.1            10.1.10.11          Master
      node 1              10.1.1.2            10.1.10.21           node
      node 2              10.1.1.3            10.1.10.22           node
==================  ====================  ====================  ============


For this setup we will use the steps specified here: `Ubuntu getting started guide 16.04 <http://kubernetes.io/docs/getting-started-guides/kubeadm/>`_

For ubuntu version earlier than 15, you will need to refer to this process: `Ubuntu getting started guide <http://kubernetes.io/docs/getting-started-guides/ubuntu/manual/>`_

To install Kubernetes on our ubuntu systems, we will leverage **kubeadm**

Here are the steps that are involved:

1. make sure that firewalld is disabled (not supported today with kubeadm)
2. disable Apparmor 
3. add google repo to your environment (**already done in UDF blueprints** see :ref:`source_list`) - on all systems
3. install docker if not already done (many kubernetes services will run into containers for reliability)
4. install kubernetes packages

to make sure the systems are up to date, run this command on **all systems**:

::

	sudo apt-get update && sudo apt-get upgrade -y

installation
-------------

once this is done, install docker if not already done on **all systems**:

::

	apt-get install -y docker.io 

then install our needed kubernetes packages on **all systems**:

::

	apt-get install -y kubelet kubeadm kubectl kubernetes-cni
	

Limitations:
-----------

for a full list of the limitations go here: `kubeadm limitations <http://kubernetes.io/docs/getting-started-guides/kubeadm/#limitations>`_

* the cluster created here has a single master, with a single etcd database running on it. This means that if the master fails, your cluster loses its configuration data and will need to be recreated from scratch