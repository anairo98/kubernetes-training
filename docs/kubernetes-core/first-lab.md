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

- **Podman** or **Docker**: For building, running, and pushing container images
- **Minikube**: As a local light weight kubernetes cluster for deploying the container images (builds up a cluster on a single node)

Instruction to install **Podman** or **Docker** and **Minikube** on different operating systems can be find in their official documentation:

[Podman Installation Instructions](https://podman.io/docs/installation)

[Install Docker Engine](https://docs.docker.com/engine/install/)

[minikube start](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)

--->>> ***Set Alias*** <<<---

Do not forget to set the alias for the ***minikube kubectl*** command in the **~/.bashrc** as:

```bash
alias kubectl="minikube kubectl --"
```

Otherwise, you should type the whole *"minikube kubectl"* every time you want to see resources on minikube.

--->>> ***Set Alias*** <<<---

### Steps
Go to the corresponding directory start building and deploying the images with *podman* or *docker*.

**First** --> *"static_joke_webpage"* 

**Second** --> *"image_accept_webpage"*

For each of the above application, go thorough these steps:

#### 1. Build the Image with Podman/Docker

Run the following command in the directory containing your Containerfile. 

> Thereby you may need to clone the Github Repository with all necessary files prior to the following commands. 

```bash
podman build -t <YOURNAME_image-name>:<YOURNMAE> .

# If you use docker instead: 

docker build -f ./Containerfile -t <YOURNAME_image-name>:<YOURNAME> .
```

Replace `<YOURNAME_image-name>` and `<tag>` with your desired image name and tag.

> Validate if the image was build up:

```bash
podman images

# If you use docker instead:

docker images
```

#### 2. Run the Image with Podman/Docker (Create a Container)

To test the image locally, use:

```bash
podman run -d -p 8080:8080 <YOURNAME_image-name>:<tag>

# If you use docker instead:
docker run -d -p 8080:8080 <YOURNAME_image-name>:<tag>
```

This maps port 8080 (first port number) on your host to port 8080 (second port number) in the container. You can set an arbitrary port for the host, but container port **MUST** be 8080.

> Validate if the container is running:

```bash
podman ps

# If you use docker instead:

docker ps
```

#### 3. Push the Image to a Registry

- Log in to your container registry (e.g., Docker Hub, Quay.io). It will ask you for your password:

```bash
podman login <registry-url> -u <username> 

# If you use docker instead: 

docker login <registry-url> -u <username> 
```

> The <registry-url> is in the case of the *Kubernetes Basic Training* **docker.io** registry

- Then push the image you just built:

```bash
# Push image to Docker Hub into an separate repository just for the newly build image:
podman push <YOURNAME_image-name>:<tag> <registry-url>/<REGISTRY-USERNAME>/<YOURNAME_image-name>:<tag>

# If you use docker instead and want to push image to Docker Hub into an separate repository just for the newly build image:

docker push <registry-url>/<REGISTRY-USERNAME>/<YOURNAME_image-name>:<tag>

# If you use docker instead and want to push the image into an existing Repository in your Docker Hub:

docker tag <YOURNAME_image-name>:<YOURNAME> <REGISTRY.USERNAME>/<REPOSITORYNAME>:<YOURNAME>
docker push <DOCKERHUB-USERNAME>/<REPOSITORYNAME>:<YOURNAME>
```

> The repository names: <REPOSITORYNAME> are the following:

- For the *"static_joke_webpage"*: **static-joke**
- For the *"image_accept_webpage"*: **image-repository** 

        
> Replace placeholders with your actual values as needed.