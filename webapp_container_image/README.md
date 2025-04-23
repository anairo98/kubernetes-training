## Webapp in Container
This tutorial provides step-by-step instructions for containerizing a web application, building its container image, and deploying it to a local Kubernetes cluster using Minikube. By following this guide, you will learn how to use Podman for container image management and Kubernetes for application deployment.

### Directory Structure

The `webapp_container_image` directory contains the following files and subdirectories:

```
webapp_container_image/
├── static_joke_webpage/
    ├── Dockerfile          # Defines the container image build instructions.
    ├── html/
        ├── index.html      # Contains the information and logics of the static webpage.
├── image_accept_webpage/
    ├── Dockerfile          # Defines the container image build instructions.
    ├── html/
        ├── index.html      # Contains the information and logics of the static webpage.
└── README.md               # Documentation with instructions to build, run, push, and deploy the image. 
```

### Prerequisites
Ensure you have the following tools installed and configured:
- **Podman**: For building, running, and pushing container images.
- **Minikube**: As a local light weight kubernetes cluster for deploying the container images.

### Steps
Go to the corresponding directory start building and deploying the images with podman.

**First** --> *"static_joke_webpage"* 

**Second** --> *"image_accept_webpage"*.

For each of the above application, go thorough these steps:

#### 1. Build the Image with Podman

Run the following command in the directory containing your Dockerfile:
```bash
podman build -t <image-name>:<tag> .
```
Replace `<image-name>` and `<tag>` with your desired image name and tag.

#### 2. Run the Image with Podman
To test the image locally, use:
```bash
podman run -d -p 8080:80 <image-name>:<tag>
```
This maps port 8080 on your host to port 80 in the container.

#### 3. Push the Image to a Registry
Log in to your container registry (e.g., Docker Hub, Quay.io):
```bash
podman login <registry-url>
```
Then push the image:
```bash
podman push <image-name>:<tag> <registry-url>/<repository>/<image-name>:<tag>
```

#### 4. Deploy the Image Locally Using Minikube
1. Start Minikube:
    ```bash
    minikube start
    ```
2. Load the image into Minikube:
    ```bash
    minikube image load <image-name>:<tag>
    ```
3. Load the image into Minikube:
    ```bash
    minikube image load <image-name>:<tag>
    ```

4. Create a Kubernetes deployment:
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: webapp
    spec:
      replicas: 1
      selector:
         matchLabels:
            app: webapp
      template:
         metadata:
            labels:
              app: webapp
         spec:
            containers:
            - name: webapp
              image: <image-name>:<tag>
              ports:
              - containerPort: 80
    ```
    Save this YAML to a file (e.g., `deployment.yaml`) and apply it:
    ```bash
    kubectl apply -f deployment.yaml
    ```
5. Expose the deployment as a service:
    ```bash
    kubectl expose deployment webapp --type=NodePort --port=80
    ```
6. Access the service:
    ```bash
    minikube service webapp
    ```

Replace placeholders with your actual values as needed.
