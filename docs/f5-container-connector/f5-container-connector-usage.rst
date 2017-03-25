Container Connector Usage
=========================

Now that our container connector is up and running, let's deploy an application and leverage our CC. 

if you don't use UDF, you can deploy any application you want. In UDF, the blueprint already has a container called f5-demo-app already loaded as an image (Application provided by Eric Chen - F5 Cloud SA). It is loaded in our container registry 10.1.10.11:5000/f5-demo-app

To deploy our front-end application, we will need to do the following:

* Define a deployment: this will launch our application running in a container
* Define a ConfigMap: ConfigMap can be used to store fine-grained information like individual properties or coarse-grained information like entire config files or JSON blobs. It will contain the BIG-IP configuration we need to push
* Define a Service: A Kubernetes *service* is an abstraction which defines a logical set of *pods* and a policy by which to access them. expose the *service* on a port on each node of the cluster (the same port on each *node*). You’ll be able to contact the service on any <NodeIP>:NodePort address. If you set the type field to "NodePort", the Kubernetes master will allocate a port from a flag-configured range **(default: 30000-32767)**, and each Node will proxy that port (the same port number on every Node) into your *Service*. 

App Deployment
--------------

On the **master** we will create all the required files: 

Create a file called my-frontend-deployment.yaml: 

::

	apiVersion: extensions/v1beta1
	kind: Deployment
	metadata:
	  name: my-frontend
	spec:
	  replicas: 2
	  template:
	    metadata:
	      labels:
	        run: my-frontend
	    spec:
	      containers:
	      - image: "10.1.10.11:5000/f5-demo-app"
	        env:
	        - name: F5DEMO_APP
	          value: "frontend"
	        - name: F5DEMO_BACKEND_URL
	          value: "http://my-backend/"
	        imagePullPolicy: IfNotPresent
	        name: my-frontend
	        ports:
	        - containerPort: 80
	          protocol: TCP

Create a file called my-frontend-configmap.yaml:

::

	kind: ConfigMap
	apiVersion: v1
	metadata:
	  name: my-frontend
	  namespace: default
	  labels:
	    f5type: virtual-server
	data:
	  schema: "f5schemadb://bigip-virtual-server_v0.1.2.json"
	  data: |-
	    {
	      "virtualServer": {
	        "frontend": {
	          "balance": "round-robin",
	          "mode": "http",
	          "partition": "kubernetes",
	          "virtualAddress": {
	            "bindAddr": "10.1.10.80",
	            "port": 80
	          }
	        },
	        "backend": {
	          "serviceName": "my-frontend",
	          "servicePort": 80
	        }
	      }
	    }

Create a file called my-frontend-service.yaml:

::

	apiVersion: v1
	kind: Service
	metadata:
	  name: my-frontend
	  labels:
	    run: my-frontend
	spec:
	  ports:
	  - port: 80
	    protocol: TCP
	    targetPort: 80
	  type: NodePort
	  selector:
	    run: my-frontend

.. Note::

	If you use UDF, you have templates you can use in your jumpbox. It's on the Desktop > F5 > kubernetes-demo folder. If you use those files, you'll need to :
	* Update the container image path in the deployment file
	* Update the "bindAddr" in the configMap for an IP you can use in this blueprint. 

We can now launch our application : 

::

	kubectl create -f my-frontend-deployment.yaml

	kubectl create -f my-frontend-configmap.yaml

	kubectl create -f my-frontend-service.yaml

.. image:: ../images/f5-container-connector-launch-app.png
	:align: center


to check the status of our deployment, you can run the following commands: 

::

	kubectl get pods -n default 

	kubectl describe svc -n default

.. image:: ../images/f5-container-connector-check-app-definition.png
	:align: center

Here you need to pay attention to the NodePort value. That is the port used by Kubernetes to give you access to the app from the outside

Now that we have deployed our application sucessfully, we can check our BIG-IP configuration. 

.. warning::

	Don't forget to select the "kubernetes" partition or you'll see nothing


.. image:: ../images/f5-container-connector-check-app-bigipconfig.png
	:align: center

.. image:: ../images/f5-container-connector-check-app-bigipconfig2.png
	:align: center


Here you can see that the pool members listed are all the kubernetes nodes. 

Now you can try to access your application via your BIG-IP VIP: 10.1.10.80: 

.. image:: ../images/f5-container-connector-access-app.png
	:align: center

Hit Refresh many times and you should see:

* the Server IP changing, here it is 10.40.0.2 and 10.40.0.3. 

* on your **BIG-IP**, go to Local Traffic > Pools > Pool list > my-frontend_10.1.10.80_80 > Statistics to see that traffic is distributed as expected
  
 .. image:: ../images/f5-container-connector-check-app-bigip-stats.png
 	:align: center