# Athena Create API

![Logo](link-to-logo-if-any)

Athena Create API is an advanced language model API designed to seamlessly integrate and manage models like `text-davinci-003` and many more open-source language models. This API comes with the capability to run the models locally, utilize Hugging Face Inference Endpoints, or even run in a hybrid mode that uses both local and Hugging Face models.

## System Requirements

#### Recommended Configuration (Default)

+ Ubuntu 16.04 LTS
+ VRAM >= 24GB
+ RAM > 12GB (minimal), 16GB (standard), 80GB (full)
+ Disk > 284GB 

#### Minimum (Lite)
+ Ubuntu 16.04 LTS

## Configuration

The server-side configuration file is `server/configs/config.default.yaml`, and some parameters are presented as follows:

+ `model`: LLM, currently supports `text-davinci-003`. We are working on integrating more open-source LLMs.
+ `inference_mode`: mode of inference endpoints
  + `local`: only use the local inference endpoints
  + `huggingface`: only use the Hugging Face Inference Endpoints **(free of local inference endpoints)**
  + `hybrid`: both of `local` and `huggingface`
+ `local_deployment`: scale of locally deployed models, works under `local` or `hybrid` inference mode:
  +  `minimal` (RAM>12GB, ControlNet only)
  +  `standard` (RAM>16GB, ControlNet + Standard Pipelines)
  +  `full` (RAM>42GB, All registered models)

## Setup

### Docker Setup

1. Clone this repository

```
git clone https://github.com/Agora-X/Athena-Create-API.git
cd Athena-Create-API
```

2. Build the Docker image

```
docker build -t athena-create-api:latest .
```

3. Run the Docker image

```
docker run -p 8004:8004 -p 8005:8005 athena-create-api:latest
```

### Kubernetes Deployment

#### Prerequisites

+ A Kubernetes cluster up and running.
+ `kubectl` configured to interact with your Kubernetes cluster.

1. Apply the Kubernetes deployment

```
kubectl apply -f kubernetes-deployment.yaml
```

2. Apply the Kubernetes service

```
kubectl apply -f kubernetes-service.yaml
```

3. To view the status of your deployment, use the following command:

```
kubectl get pods
```

4. Once the status of all pods is `Running`, you can access your application via the load balancer's IP address on port 8004 for model server and 8005 for the web server. 

## API Documentation

Documentation on how to use the various endpoints of the API can be found [here](link-to-documentation).

## Contributing

Contributions are welcome! Please read our [contribution guide](link-to-contribution-guide) for details.

## Share with Friends

Help us spread the word about Athena Create API by sharing this project with your friends and colleagues. Here is a quick share link:

[Share Athena Create API](https://github.com/Agora-X/Athena-Create-API)

## License

This project is licensed under the terms of the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International license.](/LICENSE)

## Contact

If you have any questions or suggestions, please feel free to open an issue [here](https://github.com/Agora-X/Athena-Create-API/issues).


# Deploying Athena Create API on AWS using Kubernetes

This guide will provide extensive and detailed instructions on how to deploy the Athena Create API on AWS using Kubernetes, configured for self-healing, auto-scaling, and other important production infrastructure features.

### Prerequisites

Before starting, ensure the following requirements are met:

1. You have an AWS account.
2. You have installed and configured the AWS CLI.
3. You have installed and configured `kubectl` and `eksctl` on your local machine.
4. You have a Docker Hub account (for storing Docker images).
5. You have Docker installed on your local machine.

### Table of Contents

1. [Build Docker Image](#build-docker-image)
2. [Push Docker Image to Docker Hub](#push-docker-image-to-docker-hub)
3. [Setup AWS EKS Cluster](#setup-aws-eks-cluster)
4. [Deploy Application](#deploy-application)
5. [Setup Load Balancer](#setup-load-balancer)
6. [Setup Auto-scaling](#setup-auto-scaling)
7. [Testing](#testing)
8. [Monitoring and Logging](#monitoring-and-logging)
9. [Cleanup](#cleanup)

## 1. Build Docker Image <a name="build-docker-image"></a>

From the root directory of the project, build the Docker image:

```bash
docker build -t athena-create-api .
```

Make sure to replace `athena-create-api` with the desired name for the Docker image.

## 2. Push Docker Image to Docker Hub <a name="push-docker-image-to-docker-hub"></a>

Tag the image with your Docker Hub username and the image name, then push it:

```bash
docker tag athena-create-api:latest yourusername/athena-create-api:latest
docker push yourusername/athena-create-api:latest
```

## 3. Setup AWS EKS Cluster <a name="setup-aws-eks-cluster"></a>

Create an EKS cluster using `eksctl`:

```bash
eksctl create cluster --name athena-create-api-cluster --region us-west-2 --nodes 3
```

Replace `us-west-2` with the desired AWS region and `3` with the desired number of nodes.

## 4. Deploy Application <a name="deploy-application"></a>

First, apply the Kubernetes secrets and configmaps:

```bash
kubectl apply -f kubernetes/athena-create-api-secret.yaml
kubectl apply -f kubernetes/athena-create-api-configmap.yaml
```

Next, update the `athena-create-api-deployment.yaml` file with the Docker image you pushed to Docker Hub:

```yaml
spec:
  containers:
  - name: athena-create-api
    image: yourusername/athena-create-api:latest
    ...
```

Then, apply the Kubernetes deployment and service:

```bash
kubectl apply -f kubernetes/athena-create-api-deployment.yaml
kubectl apply -f kubernetes/athena-create-api-service.yaml
```

## 5. Setup Load Balancer <a name="setup-load-balancer"></a>

In the `athena-create-api-service.yaml` file, make sure the service type is set to `LoadBalancer`:

```yaml
spec:
  type: LoadBalancer
  ...
```

Once the service is applied, you can get the public URL of your application with:

```bash
kubectl get service athena-create-api-service
```

## 6. Setup Auto-scaling <a name="setup-auto-scaling"></a>

Apply the Kubernetes Horizontal Pod Autoscaler:

```bash
kubectl apply -f kubernetes

/athena-create-api-hpa.yaml
```

Make sure to configure the `minReplicas`, `maxReplicas`, and `targetCPUUtilizationPercentage` fields in the `athena-create-api-hpa.yaml` file based on your needs.

## 7. Testing <a name="testing"></a>

Test the deployment by navigating to the public URL of your application from the Load Balancer.

## 8. Monitoring and Logging <a name="monitoring-and-logging"></a>

You can use Amazon CloudWatch for monitoring and logging. To access logs:

1. Open the CloudWatch console.
2. In the navigation pane, choose `Logs`.
3. In the log groups pane, choose the log group for your application.

You can also use `kubectl` commands to monitor the pods and nodes of your Kubernetes cluster.

## 9. Cleanup <a name="cleanup"></a>

To avoid incurring charges, you can delete the EKS cluster when you're done:

```bash
eksctl delete cluster --name athena-create-api-cluster
```

**Note**: Be sure to replace any placeholder values with your actual values throughout this guide. It's also important to understand that managing a Kubernetes cluster on AWS may incur charges, so always monitor your usage and costs.