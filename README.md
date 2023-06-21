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

This project is licensed under the terms of the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International license.

## Contact

If you have any questions or suggestions, please feel free to open an issue [here](https://github.com/Agora-X/Athena-Create-API/issues).
