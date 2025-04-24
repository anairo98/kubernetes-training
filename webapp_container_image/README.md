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
- **Podman** / **Docker**: For building, running, and pushing container images.
- **Minikube**: As a local light weight kubernetes cluster for deploying the container images.

Instruction to install **Podman** or **Docker** and **Minikube** on different operating systems can be find in their official documentation:

[Podman Installation Instructions](https://podman.io/docs/installation)

[Install Docker Engine](https://docs.docker.com/engine/install/)

[minikube start](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)

--->>> ***Set Alias*** <<<---

Do not forget to add set the alias for ***minikube kubectl*** as:
```bash
alias kubectl="minikube kubectl --"
```
Otherwise, you should type the whole *"minikube kubectl"* every time you want to see resources on minikube.

--->>> ***Set Alias*** <<<---

### Steps
Go to the corresponding directory start building and deploying the images with *podman* or *docker*.

**First** --> *"static_joke_webpage"* 

**Second** --> *"image_accept_webpage"*.

For each of the above application, go thorough these steps:

#### 1. Build the Image with Podman/Docker

Run the following command in the directory containing your Containerfile:
```bash
podman build -t <YOURNAME_image-name>:<tag> .
docker build -f ./Containerfile -t <YOURNAME_image-name>:<tag> .
```
Replace `<YOURNAME_image-name>` and `<tag>` with your desired image name and tag.

#### 2. Run the Image with Podman/Docker
To test the image locally, use:
```bash
podman run -d -p 8080:8080 <YOURNAME_image-name>:<tag>
docker run -d -p 8080:8080 <YOURNAME_image-name>:<tag>
```
This maps port 8080 on your host to port 8080 in the container. You can set an arbitrary port for the host, but container port **MUST** be 8080.

#### 3. Push the Image to a Registry
Log in to your container registry (e.g., Docker Hub, Quay.io). It will ask you for your password:
```bash
podman login <registry-url> -u <username> 
docker login <registry-url> -u <username> 
```
Then push the image you just built:
```bash
podman push <YOURNAME_image-name>:<tag> <registry-url>/<repository>/<YOURNAME_image-name>:<tag>
docker push <YOURNAME_image-name>:<tag> <registry-url>/<repository>/<YOURNAME_image-name>:<tag>
```

#### 4. Deploy the Image Locally Using Minikube
1. Start Minikube:
    ```bash
    minikube start
    ```
2. Load the image into Minikube and make sure that is correctly loaded:
    ```bash
    minikube image load <YOURNAME_image-name>:<tag>
    minikube ssh -- docker images
    ```
3. Deploy the image as single pod on Minikube:
    ```bash
    kubectl run <pod-name> --image=<YOURNAME_image-name>:<tag> --port 8080
    ```

<!-- 4. Create a Kubernetes deployment:
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
              image: <YOURNAME_image-name>:<tag>
              ports:
              - containerPort: 8080
    ```
    Save this YAML to a file (e.g., `deployment.yaml`) and apply it:
    ```bash
    kubectl apply -f deployment.yaml
    ``` -->

4. At this point, we have two ways to access the webpage:

    1. We can create a service with type **"ClusterIP"**:
        ```bash
        kubectl expose pod <pod-name> --type=ClusterIP --port=8080 --name <service-name>
        ```
        and then use minikube native load balancer to open a tunnel to the serice:
        ```bash
        minikube service <service-name>
        ```

    2. The second method uses the service type **"NodePort"**:
        ```bash
        kubectl expose pod <pod-name> --type=NodePort --port=8080 --name <service-name>
        ```
        and the ip address of the minikube's node, which can be obtained from this command:
        ```bash
        minikube node list
        ```
        to access the webpage. For that we need the **"nodePort"** assigned to the service we just created:
        ```bash
        kubectl get service
        ```
        as shown in this image, look for a port number that starts with ***30...***:
        [![nodeport](/img/nodeport.png)](/img/nodeport.png)
        
        In the browser go the *URL* that is built from *minikube-node-ip* and *nodeport* to see the webpage:
        ```bash
        URL = http://<minikube-node-ip>:<nodeport>
        ```
        
Replace placeholders with your actual values as needed.
