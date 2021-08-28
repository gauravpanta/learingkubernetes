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

    ```
    kubectl apply -f k8s/nginx-deployment.yaml
    ```


That's all for Day 1


## Day 2

### Kubernetes Service file 
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

```
kubectl apply -f k8s/nginx-service.yaml
```

- Get Service info 
  ```
  kubectl describe service nginx-service
  ```
- Get more pod info
  ```
  kubectl get pod -o wide
  ```

- Get full deployment yaml
  ```
  kubectl get deployment nginx-deployment -o yaml > ndres.yaml
  ```

- Delete with config file
  ```
  kubectl delete -f k8s/nginx-deployment.yaml
  ```



### Create Mongo DB deployment
Request Comes from browser -> Mongo Express External Service -> Mongo Express Pod -> Mongo DB Internal Service -> MongoDB Pod

- Create [secret config](https://github.com/gauravpanta/learningkubernetes/blob/gh-pages/k8s/mongo-secret.yaml)
  ```yaml
  apiVersion: v1
  kind: Secret
  metadata:
    name: mongodb-secret
  type: Opaque
  data:
    mongo-root-username: base64-value
    mongo-root-password: base64-value
  ```
- Create the [mongodb-deployment yaml](https://github.com/gauravpanta/learningkubernetes/blob/gh-pages/k8s/mongo-deployment.yaml) and add env's as follows
  ```yaml
  ...
          env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              key: mongo-root-username
              name: mongodb-secret
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              key: mongo-root-password
              name: mongodb-secret
  ```
- Simple way to encode values to base64
  ```
  echo -n 'gp-admin' | base64
  ```
- Applying secret is same as applying deployment
- Command to get secret
  ```
  kubectl get secret
  ```
- Apply the deployment!
  

### Next we create Internal Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```

- Getting all items in k8s
  ```
  kubectl get all | grep mongodb
  ```

### Mongo Express External Service
- Check out [k8s/mongo-express-deployment.yaml](https://github.com/gauravpanta/learningkubernetes/blob/gh-pages/k8s/mongo-express-deployment.yaml)
- Check out [k8s/mongo-configmap.yaml](https://github.com/gauravpanta/learningkubernetes/blob/gh-pages/k8s/mongo-configmap.yaml)
- Config map basically can be used to map services to a variable which can be used in Deployments
- Apply configmap yaml first and then the deployment
- Create External Service to allow connections from outside
    - External Service has type: Loadbalancer
    - It accepts nodePort config param which must be between 30000-32767
- Apply the external service 
- Assign external ip address by using
  ```
  minikube service mongodb-express-service
  ```

Bam! We just made our service (mongo-express) accessible publically without allowing direct access to our mongodb instance.

Super! Day 2 Complete!

## Day 3


### Namespaces in kubernetes
```bash
kubectl get namespaces
```

Output:
```
NAME                   STATUS   AGE
default                Active   44d
kube-node-lease        Active   44d
kube-public            Active   44d
kube-system            Active   44d
kubernetes-dashboard   Active   44d
```


```bash
kubectl cluster-info
```

### Creating new namespaces
```bash
kubectl create namespace gpdon
```

Why to use namespaces?
- To separate applications !
- To get an easier overview.
- Logically grouping resources.
- Separate teams can work on their own namespaces
- Blue/Green Deployments (A/B) in same kubernetes cluster with resource sharing (share elk or nginx or sth like that)
  
FAQ:
- Each namespace has its own configmaps
- We can access other services from multiple namespaces
- Some components cant be created in a namespace. eg: Persistent Volumes. To access these:
``` bash
kubectl api-resources --namespaced=false
```

### How to create components in a Namespace

*Add namespace attribute to metadata section* 

Create a namespace config file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <insert-namespace-name-here>
```

In mine i have saved it as k8s/gpdon-namespace.yaml. Then apply the config.

That's all for Day 3.

