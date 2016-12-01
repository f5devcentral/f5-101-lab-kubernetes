Flannel overview
================

Extract from `here <https://github.com/coreos/flannel#flannel>`_

flannel is a virtual network that gives a subnet to each host for use with container runtimes.

Platforms like Kubernetes assume that each container (pod) has a unique, routable IP inside the cluster. The advantage of this model is that it reduces the complexity of doing port mapping.

flannel runs an agent, flanneld, on **each host** and is responsible for allocating a subnet lease out of a preconfigured address space. 
flannel uses *etcd* to store the network configuration, allocated subnets, and auxiliary data (such as host's IP). The forwarding of packets is achieved using one of several strategies that are known as backends. The simplest backend is *udp* and uses a TUN device to encapsulate every IP fragment in a UDP packet, forming an overlay network. 

The following diagram demonstrates the path a packet takes as it traverses the overlay network:

.. image:: ../images/getting-started-flannel-schema.png
	:align: center
	:scale: 50%