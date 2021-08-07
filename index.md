## Welcome to Learning Kubernetes

This is a stupid blog I created with stupid markdown file with stupid github pages to track down my stupid journey of learning kubernetes as a software engineer.

I will store commands and links here so i might need it later.

## Day 1

- Install [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Command to create a simple deployment
  ```
  kubectl create deployment gp-awesome-nginx --image=nginx
  ```
- Command to view replicaset
  ```
  kubectl get replicaset
  ```

- Basically, Deployment manages a replicaset. Replicaset manages a Pod. Pod is an abstraction of a container.


- Command to edit deployment
  ```
  kubectl edit deployment gp-awesome-nginx
  ```


- Command to get pods
  ```
  kubectl get pod
  ```

### Debugging Pods

- Command to get logs
  ```
  kubectl logs -f --tail 1000 {pod-name}
  ```

- Go inside the pod
  ```
  kubectl exec -it {pod-name} -- bash
  ```

### Delete Pods
    ```
    kubectl delete deployment gp-awesome-nginx
    ```

### Kubernetes Configuration File
  - Create a basic deployment file with `touch nginx-deployment.yaml`
  - Add following to the file using your favourite text editor



  ```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 80
  ```

    ```bash
    kubectl apply -f k8s/nginx-deployment.yaml
    ```


That's all for Day 1