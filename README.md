# SampleMERNwithMicroservices

## Step 1: Set Up the AWS Environment

### 1.1 Set Up AWS CLI and Boto3
- Install AWS CLI and configure it with AWS credentials.
  - To log in to the AWS CLI, create an access key from IAM, download it, and save it securely.
  - Configure AWS CLI by entering the access key, secret access key, region, and format as JSON using the `aws configure` command.
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/19ab7c3a-cd7f-4a13-9692-3316b2bcd3f2)
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/04e0bcb3-269a-4dbf-b6dc-285a692cb2c0)
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/d760b6e6-d9b9-4c54-8430-ccfa31892ef3)
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/8d48858a-0b15-4ccd-85ac-e6968fea14aa)
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/04f8bbd5-d85b-4b7d-9464-ac808a7e78f6)
  - ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/08db6623-20ff-4573-986b-2ffc85baa85c)
- Type AWS configure in the EC2 instance and provide the Access key, Secret Access Key, the region, and the format as JSON. Once you have added the access key successfully, you can confirm by typing again using AWS configure and you will be able to see the same as shown in the below screenshot.
  -	Aws configure
  -	![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/f99f3833-bb04-45dd-a74a-4824c2d476f5)
  -	- Install Boto3 for Python and configure it.
  ```bash
  pip install boto3

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
