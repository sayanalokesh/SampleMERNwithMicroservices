# SampleMERNwithMicroservices


# EKS Deployment via HELM

Before using Helm we will individually deploy each microservice after that will deploy the whole application using HELM.

## Deploying Hello Microservice

Imperative approach-
```
kubectl create deployment hello --image=public.ecr.aws/c3w1m1q2/lokeshhelloservice --replicas=3 --port=3001
```
Declarative approach
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: hello
  name: hello
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
      - image: public.ecr.aws/c3w1m1q2/lokeshhelloservice
        name: hello-microservice
        ports:
        - containerPort: 3001
```
Now creating service to expose the deployment

Imperative approach-
```
kubectl expose deployment hello --port=3001 --target-port=3001 --type=LoadBalancer --name=hello-svc
```
Declarative approach
```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: hello
  name: hello-svc
spec:
  ports:
  - port: 3001
    protocol: TCP
    targetPort: 3001
  selector:
    app: hello
  type: LoadBalancer
status:
  loadBalancer: {}
```

## Deploying Profile Microservice

This Microservice is using credential of MongoDB database to handle that will be using secrets

Creating Secret to handle the MONGO DB credientials
```
kubectl create secret generic mongo-secret --from-literal=MONGO_URL=" your mongo db url"
```
Deployment file of Profile Microservice
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: profile-service-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: profile-service
  template:
    metadata:
      labels:
        app: profile-service
    spec:
      containers:
        - name: profile-service-container
          image: public.ecr.aws/c3w1m1q2/lokeshprofileservice
          ports:
            - containerPort: 3002
          env:
            - name: MONGO_URL
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: MONGO_URL
```
Now expose the Deployment to Loadbalancer service
```
kubectl expose deployment profile-service-deployment --type=LoadBalancer --port=3002 --target-port=3002 --name=profile-service
```

## Deploying Frontend Microservice

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend-container
          image: public.ecr.aws/c3w1m1q2/lokeshfrontend
          ports:
            - containerPort: 3000  
          env:
            - name: REACT_APP_API_URL
              value: "http://your- profile-loadbalancer-service-dns"
            - name: REACT_APP_API_HELLO
              value: "http://your-hello-loadbalancer-service-dns"
```

```
kubectl expose deployment frontend-deployment --type=LoadBalancer --port=3000 --target-port=3000 --name=frontend-service
```


## Deploying Helm Chart

```
helm install mern-microservice-release ./path/to/my-multiservice-chart
```
