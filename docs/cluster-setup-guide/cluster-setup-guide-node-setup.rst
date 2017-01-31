Node setup
==========

Once the master is setup and running, we need to connect our *nodes* to it. 


Setup Kubelet
-------------

by default kubelet will define its *Node IP* based on its name resolution. If it fails, it will use the first interface. However on a system with multiple interfaces, you may want to force a specific IP to be used. 

to force a specific IP address to be used, you can edit **/etc/systemd/system/kubelet.service.d/10-kubeadm.conf** and edit the following line:

::

	Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin"

add the following arg to it: --node-ip=<IP>

for *node1*, this should look like: 

::

	Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin --node-ip=10.1.20.21"

for *node2*, this should look like:

::

	Environment="KUBELET_NETWORK_ARGS=--network-plugin=cni --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/opt/cni/bin --node-ip=10.1.20.22"


if you want to review all *kubelet* options, you may check this link: `Kubelet <http://kubernetes.io/docs/admin/kubelet/>`_

Join the master
---------------

to join the master we need to run the command highlighted during the master initialization. In our setup it was:

::

	kkubeadm join --token=62468f.9dfb3fc97a985cf9 10.1.10.11


the output should be like this :

.. image:: ../images/cluster-setup-guide-node-setup-join-master.png
	:align: center


to make sure that your *nodes* have joined, you can run this command on the *master*:

::

	 kubectl get nodes

You should see your cluster (ie *master* + *nodes*)

.. image:: ../images/cluster-setup-guide-node-setup-check-nodes.png
	:align: center


Check that all the services are started as expected (run on the **master**): 

::

	kubectl get pods --all-namespaces

.. image:: ../images/cluster-setup-guide-node-setup-check-services.png
	:align: center

Here we see that some weave net containers keep restarting. This is due to our multi nic setup. Check this link: `UDeploying Kubernetes 1.4 on Ubuntu Xenial with Kubeadm <https://dickingwithdocker.com/deploying-kubernetes-1-4-on-ubuntu-xenial-with-kubeadm/>`_

You can validate this by connecting to a node and check the logs for the relevant container

.. image:: ../images/cluster-setup-guide-node-setup-crash-weave.png
	:align: center

to fix this, you need to run the following command on the **master**: 

::

	apt-get install -y jq

	kubectl -n kube-system get ds -l 'component=kube-proxy' -o json | jq '.items[0].spec.template.spec.containers[0].command |= .+ ["--cluster-cidr=10.32.0.0/12"]' | kubectl apply -f - && kubectl -n kube-system delete pods -l 'component=kube-proxy'

.. image:: ../images/cluster-setup-guide-node-setup-crash-weave-fix.png
	:align: center
	:scale: 50%

Once this is done, you may check that everything is in a stable "Running" state: 

::

	kubectl get pods --all-namespaces

.. image:: ../images/cluster-setup-guide-node-setup-check-all-ok.png
	:align: center

If you want to enable Kubernetes UI, you may install the dashboard. Run the following command on the **master**

::

	kubectl create -f https://rawgit.com/kubernetes/dashboard/master/src/deploy/kubernetes-dashboard.yaml

You should see the following output: 

::
	deployment "kubernetes-dashboard" created
	service "kubernetes-dashboard" created

to access the dashboard, you need to see on which port it is listening. You can find this information with the following command (on the **master**):

::

	kubectl describe svc kubernetes-dashboard -n kube-system

.. image:: ../images/cluster-setup-guide-check-port-ui.png
	:align: center	

Here we can see that it is listening on port: 31578 (NodePort)

We can now access the dashboard by connecting to the following uri http://<master IP>:31578

.. image:: ../images/cluster-setup-guide-access-ui.png
	:align: center
	:scale: 50%