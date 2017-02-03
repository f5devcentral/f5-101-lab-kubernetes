F5 ASP and kube-proxy Deployment
================================

Load images
-----------

on **all systems**

Load images 

root@node1:~# gunzip f5-kube-proxy-v1.3.7_f5.1.tar.gz
root@node1:~# gunzip light-weight-proxy-v0.2.0.tar.gz
root@node1:~# docker load -i light-weight-proxy-v0.2.0.tar
root@node1:~# docker load -i f5-kube-proxy-v1.3.7_f5.1.tar


docker images

f5-asp-and-kube-proxy-load-f5-containers.png


Deploy ASP 
----------

Configmap - vi f5-asp-configmap.yaml


::

	kind: ConfigMap
	apiVersion: v1
	metadata:
	  name: lwp-config
	  namespace: kube-system
	data:
	  lightweight-proxy.config.json: |-
	    {
	      "global": {
	        "console-log-level": "info"
	      },
	      "orchestration": {
	        "kubernetes": {
	          "config-file": "/var/run/kubernetes/proxy-plugin/service-ports.json",
	          "poll-interval": 500
	        }
	      }
	    }



Daemonset -  vi f5-asp-daemonset.yaml

::

	apiVersion: extensions/v1beta1
	kind: DaemonSet
	metadata:
	  name: lightweight-proxy
	  namespace: kube-system
	spec:
	  template:
	    metadata:
	      labels:
	        name: lightweight-proxy
	    spec:
	      hostNetwork: true
	      containers:
	        - name: proxy-plugin
	          image: f5networks/f5-ci-beta:light-weight-proxy-v0.2.0
	          args:
	            - --config-file
	            - /etc/configmap/lightweight-proxy.config.json
	          securityContext:
	            privileged: false
	          volumeMounts:
	          - mountPath: /var/run/kubernetes/proxy-plugin
	            name: plugin-config
	            readOnly: true
	          - mountPath: /etc/configmap
	            name: lwp-config
	      volumes:
	        - name: plugin-config
	          hostPath:
	            path: /var/run/kubernetes/proxy-plugin
	        - name: lwp-config
	          configMap:
	            name: lwp-config


 kubectl create -f f5-asp-configmap.yaml

  kubectl create -f f5-asp-daemonset.yaml


kubectl get pods -n kube-system

  f5-asp-and-kube-proxy-deploy-asp.png


Deploy f5-kube-proxy
--------------------

retrieve kube-proxy daemonset config

 kubectl edit ds kube-proxy -n kube-system

:w /tmp/kube-proxy-origin.yaml


create a new deamonset yaml: 

vi  /tmp/f5-kube-proxy-ds.yaml

::

	# Please edit the object below. Lines beginning with a '#' will be ignored,
	# and an empty file will abort the edit. If an error occurs while saving this file will be
	# reopened with the relevant failures.
	#
	apiVersion: extensions/v1beta1
	kind: DaemonSet
	metadata:
	  annotations:
	    kubectl.kubernetes.io/last-applied-configuration: '{"apiVersion":"extensions/v1beta1","kind":"DaemonSet","metadata":{"annotations":{},"creationTimestamp":"2017-01-31T10:43:01Z","generation":3,"labels":{"component":"kube-proxy","k8s-app":"kube-proxy","kubernetes.io/cluster-service":"true","name":"kube-proxy","tier":"node"},"name":"kube-proxy","namespace":"kube-system","resourceVersion":"278413","selfLink":"/apis/extensions/v1beta1/namespaces/kube-system/daemonsets/kube-proxy","uid":"09f08c86-e7a2-11e6-b1ea-525400ce18b9"},"spec":{"selector":{"matchLabels":{"component":"kube-proxy","k8s-app":"kube-proxy","kubernetes.io/cluster-service":"true","name":"kube-proxy","tier":"node"}},"template":{"metadata":{"annotations":{"scheduler.alpha.kubernetes.io/affinity":"{\"nodeAffinity\":{\"requiredDuringSchedulingIgnoredDuringExecution\":{\"nodeSelectorTerms\":[{\"matchExpressions\":[{\"key\":\"beta.kubernetes.io/arch\",\"operator\":\"In\",\"values\":[\"amd64\"]}]}]}}}","scheduler.alpha.kubernetes.io/tolerations":"[{\"key\":\"dedicated\",\"value\":\"master\",\"effect\":\"NoSchedule\"}]"},"creationTimestamp":null,"labels":{"component":"kube-proxy","k8s-app":"kube-proxy","kubernetes.io/cluster-service":"true","name":"kube-proxy","tier":"node"}},"spec":{"containers":[{"command":["/proxy","--kubeconfig=/run/kubeconfig"],"image":"f5networks/f5-ci-beta:f5-kube-proxy-v1.3.7_f5.1","imagePullPolicy":"IfNotPresent","name":"kube-proxy","resources":{},"securityContext":{"privileged":true},"terminationMessagePath":"/dev/termination-log","volumeMounts":[{"mountPath":"/var/run/dbus","name":"dbus"},{"mountPath":"/run/kubeconfig","name":"kubeconfig"},{"mountPath":"/var/run/kubernetes/proxy-plugin","name":"plugin-config"}]}],"dnsPolicy":"ClusterFirst","hostNetwork":true,"restartPolicy":"Always","securityContext":{},"terminationGracePeriodSeconds":30,"volumes":[{"hostPath":{"path":"/etc/kubernetes/kubelet.conf"},"name":"kubeconfig"},{"hostPath":{"path":"/var/run/dbus"},"name":"dbus"},{"hostPath":{"path":"/var/run/kubernetes/proxy-plugin"},"name":"plugin-config"}]}}},"status":{"currentNumberScheduled":3,"desiredNumberScheduled":3,"numberMisscheduled":0,"numberReady":3}}'
	  creationTimestamp: 2017-02-02T14:12:27Z
	  generation: 1
	  labels:
	    component: kube-proxy
	    k8s-app: kube-proxy
	    kubernetes.io/cluster-service: "true"
	    name: kube-proxy
	    tier: node
	  name: kube-proxy
	  namespace: kube-system
	  resourceVersion: "279250"
	  selfLink: /apis/extensions/v1beta1/namespaces/kube-system/daemonsets/kube-proxy
	  uid: a0917852-e951-11e6-b1ea-525400ce18b9
	spec:
	  selector:
	    matchLabels:
	      component: kube-proxy
	      k8s-app: kube-proxy
	      kubernetes.io/cluster-service: "true"
	      name: kube-proxy
	      tier: node
	  template:
	    metadata:
	      annotations:
	        scheduler.alpha.kubernetes.io/affinity: '{"nodeAffinity":{"requiredDuringSchedulingIgnoredDuringExecution":{"nodeSelectorTerms":[{"matchExpressions":[{"key":"beta.kubernetes.io/arch","operator":"In","values":["amd64"]}]}]}}}'
        	scheduler.alpha.kubernetes.io/tolerations: '[{"key":"dedicated","value":"master","effect":"NoSchedule"}]'
	      creationTimestamp: null
	      labels:
	        component: kube-proxy
	        k8s-app: kube-proxy
	        kubernetes.io/cluster-service: "true"
	        name: kube-proxy
	        tier: node
	    spec:
	      containers:
	      - command:
	        - /proxy
	        - --kubeconfig=/run/kubeconfig
	        image: f5networks/f5-ci-beta:f5-kube-proxy-v1.3.7_f5.1
	        imagePullPolicy: IfNotPresent
	        name: kube-proxy
	        resources: {}
	        securityContext:
	          privileged: true
	        terminationMessagePath: /dev/termination-log
	        volumeMounts:
	        - mountPath: /var/run/dbus
	          name: dbus
	        - mountPath: /run/kubeconfig
	          name: kubeconfig
	        - mountPath: /var/run/kubernetes/proxy-plugin
	          name: plugin-config
	      dnsPolicy: ClusterFirst
	      hostNetwork: true
	      restartPolicy: Always
	      securityContext: {}
	      terminationGracePeriodSeconds: 30
	      volumes:
	      - hostPath:
	          path: /etc/kubernetes/kubelet.conf
	        name: kubeconfig
	      - hostPath:
	          path: /var/run/dbus
	        name: dbus
	      - hostPath:
	          path: /var/run/kubernetes/proxy-plugin
	        name: plugin-config
	status:
	  currentNumberScheduled: 3
	  desiredNumberScheduled: 3
	  numberMisscheduled: 0
	  numberReady: 3


kubectl delete -f /tmp/kube-proxy-origin.yaml

 kubectl get pods -n kube-system

f5-asp-and-kube-proxy-delete-origin-kube-proxy.png

 kubectl create -f /tmp/f5-kube-proxy-ds.yaml
 kubectl get pods -n kube-system

f5-asp-and-kube-proxy-create-f5-kube-proxy.png