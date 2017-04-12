Setup master
============

Master initialization
---------------------

The master is the system where the "control plane" components run, including etcd (the cluster database) and the API server (which the kubectl CLI communicates with). All of these components run in pods started by kubelet (which is why we had to setup docker first even on the master node)

we will setup our master node on **master**, connect to it.

to setup **master** as a Kubernetes *master*, run the following command:

::

	sudo kubeadm init --api-advertise-addresses=10.1.10.11

Here we specify:

* The IP address that should be used to advertise the master. 10.1.10.0/24 is the network for our controle plane. if you don't specify the --api-advertise-addresses argument, kubeadm will pick the first interface with a default gateway (because it needs internet access). 
  
When running the command you should see something like this:

.. image:: ../images/cluster-setup-guide-kubeadm-init-master.png
	:align: center

The initialization is successful if you see "Kubernetes master initialised successfully!"

you should see a line like this:

::

	sudo kubeadm join --token=62468f.9dfb3fc97a985cf9 10.1.10.11


This is the command to run on the node so that it registers itself with the master. Keep the secret safe since anyone with this token can add authenticated node to your cluster. This is used for mutual auth between the master and the nodes

.. warning::

	**save this command somewhere since you'll need it later**

You can monitor that the services start to run by using the command: 

::

	kubectl get pods --all-namespaces

.. image:: ../images/cluster-setup-guide-kubeadmin-init-check.png
	:align: center

kube-dns won't start until the network pod is setup. 

Network pod
-----------

You must install a *pod* network add-on so that your *pods* can communicate with each other.

**It is necessary to do this before you try to deploy any applications to your cluster**, and before* kube-dns* will start up. Note also that *kubeadm* only supports CNI based networks and therefore kubenet based networks will not work.

Here is the list of add-ons available:

* Calico
* Canal 
* Flannel 
* Romana
* Weave net

We will use Weave net as mentioned previously. To set Weave net as a network pod, you may use the command: 

::

	kubectl apply -f https://git.io/weave-kube

you should see something like this: 

::

	*daemonset "weave-net" created*



check master state 
------------------

If everything runs as expected you should have kube-dns that started successfully. To check the status of the different service, you can run the command:

::

	kubectl get pods --all-namespaces

The output should show all services as running

.. image:: ../images/cluster-setup-guide-kubeadmin-init-check-cluster-get-pods.png
	:align: center 



kubectl get pods --all-namespaces

:: 
	
	kubectl get cs

.. image:: ../images/cluster-setup-guide-kubeadmin-init-check-cluster.png
	:align: center


::
	
	kubectl cluster-info

.. image:: ../images/cluster-setup-guide-kubeadmin-init-check-cluster-info.png
	:align: center

The next step will be to have our *nodes* join the *master*