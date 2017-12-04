Node setup
==========

Once the master is setup and running, we need to connect our *nodes* to it. Please be sure you did all the steps reported in the cluster installation section on each node of the cluster.


Join the master
---------------

to join the master we need to run the command highlighted during the master initialization. In our setup it was:

::

	sudo kubeadm join --token=62468f.9dfb3fc97a985cf9 10.1.10.11


the output should be like this :

.. image:: ../images/cluster-setup-guide-node-setup-join-master.jpg
	:align: center


to make sure that your *nodes* have joined, you can run this command on the *master*:

::

	 kubectl get nodes -o wide

You should see your cluster (ie *master* + *nodes*)

.. image:: ../images/cluster-setup-guide-node-setup-check-nodes.jpg
	:align: center


Check that all the services are started as expected and everything is in a stable "running" state, running this command on the **master**: 

::

	kubectl get pods --all-namespaces -o wide

.. image:: ../images/cluster-setup-guide-node-setup-check-services.jpg
	:align: center

