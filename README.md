# SampleMERNwithMicroservices

## Table of Contents
1. [Step 1: Set Up the AWS Environment](#step-1-set-up-the-aws-environment)
   - 1.1 [Set Up AWS CLI and Boto3](#11-set-up-aws-cli-and-boto3)
2. [Step 2: Prepare the MERN Application](#step-2-prepare-the-mern-application)
   - 2.1 [Containerize the MERN Application](#21-containerize-the-mern-application)
   - 2.2 [Push Docker Images to Amazon ECR](#22-push-docker-images-to-amazon-ecr)
      - 2.2.1 [Build Docker images for the frontend and backend](#221-build-docker-images-for-the-frontend-and-backend)
      - 2.2.2 [Create an Amazon ECR repository for each image](#222-create-an-amazon-ecr-repository-for-each-image)
      - 2.2.3 [Push the Docker images to their respective ECR repositories](#223-push-the-docker-images-to-their-respective-ecr-repositories)
3. [Step 3: Version Control](#step-3-version-control)
   - 3.1 [Use AWS CodeCommit](#31-use-aws-codecommit)
4. [Step 4: Continuous Integration and Deployment with Jenkins](#step-4-continuous-integration-and-deployment-with-jenkins)
5. [Step 4: Kubernetes (EKS) Deployment via HELM](#step-4-kubernetes-eks-deployment-via-helm)

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
  -	Install Boto3 for Python and configure it.
  ```bash
  pip install boto3

## Step 2: Prepare the MERN Application

### 1. Containerize the MERN Application:

Ensure the MERN application is containerized using Docker. Create a Dockerfile for each component (frontend and backend).

- We have the application code in the Git repo, and we need to clone the repo to our EC2 instance.

  ```bash
  git clone https://github.com/sayanalokesh/SampleMERNwithMicroservices.git

You can find the backend dockerfiles [here](https://github.com/sayanalokesh/SampleMERNwithMicroservices/tree/main/backend). For the frontend [here](https://github.com/sayanalokesh/SampleMERNwithMicroservices/blob/main/frontend/dockerfile).

I have written the docker-compose file to up all the dockerfiles and push the same to the respective repositories. You can find the docker-compose file [here](https://github.com/sayanalokesh/SampleMERNwithMicroservices/blob/main/docker-compose.yml)

To run the `docker-compose` file, simply type the following command:

```bash
sudo docker-compose up -d
```
## Step 3: Version Control

### 1. Use AWS CodeCommit:

- To use AWS CodeCommit, you need to add the "AWSCodeCommitPowerUser" IAM policy to the user.
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/7f3d8254-39c5-45c8-a2df-f8807111cd01)
- Once you add the policy, you also need to create “HTTPS Git credentials for AWS CodeCommit” in the user section. You need to scroll down and click on the Generate credentials button. A CSV file will be downloaded and save it securely.
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/43ef6e6d-b442-458d-b4cd-526fed369c77)
-	Create a CodeCommit repository by clicking on Create repository.
-	![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/327e4bc9-f377-4afe-89b1-ba2f21347f36)
-	![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/a2fc5428-875a-42c1-ad14-b579eee413d7)
-	![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/cf836f34-b377-4e96-80a5-efff34425885)
- Go to the EC2 instance and execute the following commands in the Git repository:

  ```bash
  git init
  git add .
  git commit -m "Initial commit"
  git remote add codecommit https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/Lokesh_Orchestration_Scaling
  git push codecommit main
  ```
- Once you have executed the above commands, all the files from the GitHub repo will be pushed to the CodeCommit repo. You can verify this by checking the repository, as shown in the screenshot below.
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/dc682219-0a63-41ce-9d0e-a1bf5e9c946a)


# Step 4: Continuous Integration and Deployment with Jenkins

## 1. Set Up Jenkins:

- Install Jenkins on an EC2 instance. You can find the Jenkins Installation guide [here](https://github.com/sayanalokesh/Jenkins/blob/main/InstallationOfJenkins.md).

- Configure Jenkins with necessary plugins.

- Install "Publish over SSH" plugin.

## 2. Create Jenkins Jobs:

- Create Jenkins jobs for building and pushing Docker images to ECR. Follow the below steps to configure the Jenkins pipeline to push the image to the ECR and deploy the images using a Docker-compose file.
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/475a32aa-2ba8-404a-833a-b94a208ed9b7)
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/cfe3180e-352b-4a19-95fd-06a1e5365279)
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/8928bc58-7ce2-4d21-8e77-1586c4fb4dcd)
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/85ae50df-5c13-4809-b621-bf25a90cfd22)
- ![image](https://github.com/sayanalokesh/SampleMERNwithMicroservices/assets/105637305/45f9fbaa-55f0-41a8-9c8a-700dc4c7a248)


# Step 4: Kubernetes (EKS) Deployment via HELM

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
