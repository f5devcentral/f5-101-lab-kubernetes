Test ASP and F5 Kube Proxy
==========================

on **master**

vi my-backend-deployment.yaml

::
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: my-backend
spec:
  replicas: 2
  template:
    metadata:
      labels:
        run: my-backend
    spec:
      containers:
      - image: f5-demo-app:latest
        imagePullPolicy: IfNotPresent
        env:
        - name: F5DEMO_APP
          value: "backend"
        name: my-backend
        ports:
        - containerPort: 80
          protocol: TCP



vi my-backend-service.yaml 

::
apiVersion: v1
kind: Service
metadata:
  annotations:
    lwp.f5.com/config.http: |-
      {
        "ip-protocol": "http",
        "load-balancing-mode": "round-robin",
        "flags" : {
          "x-forwarded-for": true,
          "x-served-by": true
        }
      }
  name: my-backend
  labels:
    run: my-backend
spec:
  ports:
  - name: "http"
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: my-backend



kubectl create -f my-backend-deployment.yaml

kubectl create -f my-backend-service.yaml

kubectl get deployment my-backend

 kubectl describe svc my-backend

f5-asp-and-kube-proxy-deploy-app.png

Access frontend as before http://10.1.10.80

f5-asp-and-kube-proxy-test-app-backend.png

click on "Backend App"

f5-asp-and-kube-proxy-test-app-backend2.png