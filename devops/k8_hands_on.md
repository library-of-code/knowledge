# k8-hands-on

#
### Keywords
kubernetes hands-on k8 devops
#

## Accessing your kubernetes environment 

Option 1:
https://labs.play-with-k8s.com/


Option 2:
https://www.katacoda.com/alanmpitts/scenarios/playground

Follow the respective guidelines. 

## Exercise 1. Get basic cluster info 

The kubectl is a command line tool that lets you control Kubernetes clusters

  ```
  kubectl --help
  ```

  ```
  $ kubectl cluster-info
  Kubernetes master is running at https://172.17.0.58:8443
  KubeDNS is running at https://172.17.0.58:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
  ```

  ```
  $ kubectl get nodes
  NAME       STATUS   ROLES    AGE   VERSION
  minikube   Ready    master   32m   v1.17.3
  ```

  ```
  $ kubectl describe nodes
  ```



## Exercise 2. Pod 

* We shall be using the following image to run as container inside the pod. It is a containerised Go webserver. 

  gcr.io/google-samples/hello-app:1.0

  More information on the image can be found here

  https://github.com/GoogleCloudPlatform/kubernetes-engine-samples/tree/master/hello-app


* Open a file in minikube terminal and paste the following content. You may name it as my-first-pod.yaml

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-first-pod  
  spec:
    containers:
    - name: hello-app
      image: gcr.io/google-samples/hello-app:1.0
  ```

*  Run the following command 

  ```
  $ kubectl apply -f my-first-pod.yaml
  pod/my-first-pod created
  ```

* Check the Pods. You pod should be listed here. 

  ```
  $ kubectl get pods
  NAME                   READY   STATUS    RESTARTS   AGE
  my-first-pod           1/1     Running   0          5m47s  
  ```

* Check the details of this pod

```
  $ kubectl describe pod my-first-pod
```

* Get the IP of the pod 
  ```
  $ kubectl get pod my-first-pod --template={{.status.podIP}}
  ```
* Access the pod using the IP 

  ```
  $ curl <pod_ip>:8080
  ```

* Check out the logs 

  ```
  $ kubectl logs my-first-pod
  ```

* Delete the pod 
  ```
  $ kubectl delete pod my-first-pod
  ```

## Excercise 3. Namespace 

Namespaces are a way to divide cluster resources between multiple projects/users. 

* Create a namespace 
  ```
  $ kubectl create namespace my-app
  ```

* Check namespaces in K8
  ```
  $ kubectl get namespaces 
  ```


## Exercise 4. Deployment 

A Deployment provides declarative updates for Pods ReplicaSets. You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state. 


* Check out the following deployment configuration and save it in a file (my-first-deployment.yaml)

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: hello-world-deployment
    labels:
      app: hello-world
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: hello-world
    template:
      metadata:
        labels:
          app: hello-world
      spec:
        containers:
        - name: web-server
          image: gcr.io/google-samples/hello-app:1.0
  ```

* create the deployment in my-app namespace

  ```
  $ kubectl apply -f my-first-deployment.yaml -n my-app
  ```

* Checkout all the resources in my-app namespace 

  ```
  $ kubectl get all -n my-app
  ```

  Checkout the output 

  ```
  NAME                                         READY   STATUS    RESTARTS   AGE
  pod/hello-world-deployment-d56f64568-2hph8   1/1     Running   0          8s
  pod/hello-world-deployment-d56f64568-mhccf   1/1     Running   0          8s
  pod/hello-world-deployment-d56f64568-v28hn   1/1     Running   0          8s

  NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
  deployment.apps/hello-world-deployment   3/3     3            3           8s

  NAME                                               DESIRED   CURRENT   READY   AGE
  replicaset.apps/hello-world-deployment-d56f64568   3         3         3       8s
  ```

* Try deleting one of the pod 
  ```
  $ kubectl delete  pod <pod_name> -n my-app
  ```

* Manually scale up/down the replicas by editing the deployment configuration. Edit the file my-first-deployment.yaml and change the replica count. Run the command again. 

  ```
  $ kubectl apply -f my-first-deployment.yaml -n my-app
  ```

* Check the status of the replicas now. You should now have the desired number. 
  ```
  $ kubectl get all -n my-app
  ```

## Excercise 5: Service 
Service is an abstraction that exposes a set of pods using a single a service IP address.  This hides the backend details, the number of running pod replicas etc. 

* Open a file (my-first-service.yaml) and paste the content below.

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: hello-app-service
  spec:
    selector:
      app: hello-world
    ports:
      - port: 80
        targetPort: 8080
  ```

* Create the service 

  ```
  kubectl apply -f my-first-service.yaml -n my-app
  ```

* Get the service IP 

  ```
  kubectl get service -n my-app
  ```

* Try accessing the service now. 

  ```
  curl <service_ip>
  ```

* By default the service is accessible on a clusterIP. This means that the service is only accessible within the cluster. What if we want to access the service outside the kubernetes cluster. 


* Create a new service. Or update the old one. Here, we are creating a new service. 

  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: hello-app-service-exposed
  spec:
    type: NodePort
    selector:
      app: hello-world
    ports:
      - port: 80
        targetPort: 8080
        nodePort: 30007
  ```

* Access the Service from outside the cluster. 

  If you are excercising the tutorial on play-with-k8s.com, you would need to copy the node URL above the terminal. And add the port :30007 to the URL to access the service. 
  
  ```
  http://ip172-18-0-11-bsvevsuj2b7000f9euj0.direct.labs.play-with-k8s.com:30007/
  ```
  
  
  If you are excercising the tutorial on Katacoda, you would need to click on the + sign at top of the terminal and follow "Select Port to view on Host 1". It shall open a new page, and there you would need to enter port number 30007 to access the service. 

  ```
  https://2886795298-30007-cykoria04.environments.katacoda.com/
  ```

Note: You may access the Service using either of the Machines. NodePort is opened on all the nodes in the cluster. 

* Try Manupulating the running pods by scaling up or scaling down the deployment and see how service adjust to the changes. 


* Free up some space. Delete the namespace. 

```
kubectl delete namespace my-app
```

## Excercise 6: Persistant volumes

By default containers are stateless, upon restarts the state is lost. Kubernetes provides a way to create and attach persistent storage to the pods so that desipte failures application state is not impacted. There are many ways in which persistant storage can be created. Follow steps uses the node's storage. 


* Create an index.html file on your Node2/Terminal2. 

  ```
  mkdir /mnt/data
  ```


  In the /mnt/data directory, create an index.html file:

  ```
  sudo sh -c "echo 'Hello from Kubernetes storage' > /mnt/data/index.html"
  ```

* Create a PersistentVolume. Open a file pv-volume.yaml and add the below content. 

  ```
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: task-pv-volume
    labels:
      type: local
  spec:
    storageClassName: manual
    capacity:
      storage: 10Mi
    accessModes:
      - ReadWriteOnce
    hostPath:
      path: "/mnt/data"
  ```
  
  ```
  $ kubectl apply -f pv-volume.yaml
  ```
  
  ```
  $ kubectl get pv
  ```
  
* Create a persistentVolumeClaim. Open a file pv-claim.yaml and add the below content.

  ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: task-pv-claim
  spec:
    storageClassName: manual
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 5Mi
  ```

  ```
  $ kubectl apply -f pv-claim.yaml
  ```
  
  ```
  $ kubectl get pvc
  ```
  
*  Create a pod. You know already know how the drill :) 

``` 
  apiVersion: v1
  kind: Pod
  metadata:
    name: task-pv-pod
  spec:
    volumes:
      - name: task-pv-storage
        persistentVolumeClaim:
          claimName: task-pv-claim
    containers:
      - name: task-pv-container
        image: nginx
        ports:
          - containerPort: 80
            name: "http-server"
        volumeMounts:
          - mountPath: "/usr/share/nginx/html"
            name: task-pv-storage
```

*  Get the IP of the Pod and do a curl. You know it already. 


# Excercise 7: Resource Allocation 

my-stressed-pod.yaml 
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: cpu-demo
  spec:
    containers:
    - name: cpu-demo-ctr
      image: vish/stress
      resources:
        limits:
          cpu: "50m"
        requests:
          cpu: "10m"
      args:
      - -cpus
      - "1"
  ```

* Create the Pod 

  ```
  kubectl apply -f my-stressed-pod.yaml
  ```

* Check the CPU of the pod. But first install metrics-server in the cluster. 
  ```
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.7/components.yaml
  ```

* Check the stats
  ```
  kubectl top pods
  ```

  ```
  kubectl top nodes
  ```

## Excercise 8 - Deploying an application consisting of multiple microservices

Goal of this excercise is to deploy a multi container application on kubernetes and expose it to external world. For this purpose, we are going to use Acme-air application. Acme-Air is a fictious airline application consisting of 8 microservices (1 UI, 4 Rest API servers and 3 databases) [Acme-Air!](https://github.com/blueperf/). 


* Architecture 

![alt text](https://github.ibm.com/mudiverm/k8-hands-on/blob/master/acmeair/image/AcmeairMS.png "AcmeairMS")


* clone this repo. And go to the directory content/acme-air

```
git clone https://github.com/mudverma/winterschool.git
cd winterschool/content/acme-air 
```


There are 10 yamls files and 2 shell scripts. Please go through the content of each file and try to correlate it with the theory class. 

```
$ ls
acmeair-authservice-ingress.yaml		
acmeair-flightservice-ingress.yaml		
deploy-acmeair-authservice-java.yaml		
deploy-acmeair-flightservice-java.yaml
acmeair-bookingservice-ingress.yaml		
acmeair-mainservice-ingress.yaml		
deploy-acmeair-bookingservice-java.yaml		
deploy-acmeair-mainservice-java.yaml
acmeair-customerservice-ingress.yaml		
deploy-acmeair-customerservice-java.yaml	
deployToMinikube.sh
deleteAcmeAir.sh
```

A sample deployment configuration for bookingservice and bookingdb is here

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: acmeair-booking-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      name: acmeair-booking-deployment
  template:
    metadata:
      labels:
        name: acmeair-booking-deployment
        service: acmeair-bookingservice
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9080"
    spec:
      containers:
      - name: acmeair-booking-deployment
        image: muditverma/hcwc-bookingservice:latest
        ports:
          - containerPort: 9080
          - containerPort: 9443
        imagePullPolicy: IfNotPresent
        env:
        - name: USERNAME
          value: admin
        - name: PASSWORD
          value: password
        - name: MONGO_HOST
          value: acmeair-booking-db
        - name: JVM_ARGS
          value: "-DcustomerClient/mp-rest/url=http://acmeair-customer-service:9080 -DflightClient/mp-rest/url=http://acmeair-flight-service:9080 -Dmp.jwt.verify.publickey.location=http://acmeair-auth-service:9080/getJwk"
        - name: TRACK_REWARD_MILES
          value: 'true'
        - name: SECURE_SERVICE_CALLS
          value: 'true'
        readinessProbe:
          httpGet:
            path: /health
            port: 9080
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /health
            port: 9080
          initialDelaySeconds: 120
          periodSeconds: 15
---
apiVersion: v1
kind: Service
metadata:
  name: acmeair-booking-service
  namespace: mudit
spec:
  ports:
    - port: 9080
  selector:
    name: acmeair-booking-deployment
---
##### Booking Database  #####
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    service: acmeair-booking-db
  name: acmeair-booking-db
spec:
  ports:
  - name: "27017"
    port: 27017
    protocol: TCP
    targetPort: 27017
  selector:
    service: acmeair-booking-db
status:
  loadBalancer: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  name: acmeair-booking-db
spec:
  replicas: 1
  selector:
    matchLabels:
      service: acmeair-booking-db
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        service: acmeair-booking-db
    spec:
      containers:
      - image: muditverma/hcwc-mongodb:latest
        name: acmeair-booking-db
        ports:
        - containerPort: 27017
          protocol: TCP
        resources: {}
      restartPolicy: Always
status: {}

```

### 3. Deploy the microservices to Kubernetes 

Note: It might take sometime to pull the images from the repos and create pods/containers.  Run the following script 

bash deployToMinikube.sh
```
#!/bin/bash
kubectl apply -f deploy-acmeair-mainservice-java.yaml
kubectl apply -f deploy-acmeair-authservice-java.yaml
kubectl apply -f deploy-acmeair-bookingservice-java.yaml
kubectl apply -f deploy-acmeair-customerservice-java.yaml
kubectl apply -f deploy-acmeair-flightservice-java.yaml

kubectl apply -f  acmeair-authservice-ingress.yaml
kubectl apply -f  acmeair-mainservice-ingress.yaml
kubectl apply -f  acmeair-bookingservice-ingress.yaml
kubectl apply -f  acmeair-customerservice-ingress.yaml
kubectl apply -f  acmeair-flightservice-ingress.yaml
```


Expect to see the following output 
```
deployment.apps/acmeair-main-deployment created
service/acmeair-main-service created
deployment.apps/acmeair-auth-deployment created
service/acmeair-auth-service created
deployment.apps/acmeair-booking-deployment created
service/acmeair-booking-service created
service/acmeair-booking-db created
deployment.apps/acmeair-booking-db created
deployment.apps/acmeair-customer-deployment created
service/acmeair-customer-service created
service/acmeair-customer-db created
deployment.apps/acmeair-customer-db created
deployment.apps/acmeair-flight-deployment created
service/acmeair-flight-service created
service/acmeair-flight-db created
deployment.apps/acmeair-flight-db created
ingress.extensions/acmeair-auth-ingress created
ingress.extensions/acmeair-main-ingress created
ingress.extensions/acmeair-booking-ingress created
ingress.extensions/acmeair-customer-ingress created
ingress.extensions/acmeair-flight-ingress created
```


### 4. Check the status periodically to see the progress 
```
$ kubectl get all
NAME                                              READY   STATUS    RESTARTS   AGE
pod/acmeair-auth-deployment-b696d7cf9-slzrg       1/1     Running   0          27s
pod/acmeair-booking-db-cf5b5d49f-x6znt            1/1     Running   0          27s
pod/acmeair-booking-deployment-665599b6bb-4xb9t   1/1     Running   0          27s
pod/acmeair-customer-db-755b689c79-fgp9z          1/1     Running   0          26s
pod/acmeair-customer-deployment-65f898bf6-p5l22   1/1     Running   0          27s
pod/acmeair-flight-db-55dd58dbc9-p6dkr            1/1     Running   0          26s
pod/acmeair-flight-deployment-69b776b47f-8ldm2    1/1     Running   0          26s
pod/acmeair-main-deployment-646875f86d-gx2n2      1/1     Running   0          27s

NAME                               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/acmeair-auth-service       ClusterIP   10.96.87.148    <none>        9080/TCP    27s
service/acmeair-booking-db         ClusterIP   10.96.156.122   <none>        27017/TCP   27s
service/acmeair-booking-service    ClusterIP   10.96.222.146   <none>        9080/TCP    27s
service/acmeair-customer-db        ClusterIP   10.96.76.79     <none>        27017/TCP   26s
service/acmeair-customer-service   ClusterIP   10.96.214.186   <none>        9080/TCP    26s
service/acmeair-flight-db          ClusterIP   10.96.68.33     <none>        27017/TCP   26s
service/acmeair-flight-service     ClusterIP   10.96.156.92    <none>        9080/TCP    26s
service/acmeair-main-service       ClusterIP   10.96.113.177   <none>        9080/TCP    27s
service/kubernetes                 ClusterIP   10.96.0.1       <none>        443/TCP     3m24s

NAME                                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/acmeair-auth-deployment       1/1     1            1           27s
deployment.apps/acmeair-booking-db            1/1     1            1           27s
deployment.apps/acmeair-booking-deployment    1/1     1            1           27s
deployment.apps/acmeair-customer-db           1/1     1            1           26s
deployment.apps/acmeair-customer-deployment   1/1     1            1           27s
deployment.apps/acmeair-flight-db             1/1     1            1           26s
deployment.apps/acmeair-flight-deployment     1/1     1            1           26s
deployment.apps/acmeair-main-deployment       1/1     1            1           27s

NAME                                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/acmeair-auth-deployment-b696d7cf9       1         1         1       27s
replicaset.apps/acmeair-booking-db-cf5b5d49f            1         1         1       27s
replicaset.apps/acmeair-booking-deployment-665599b6bb   1         1         1       27s
replicaset.apps/acmeair-customer-db-755b689c79          1         1         1       26s
replicaset.apps/acmeair-customer-deployment-65f898bf6   1         1         1       27s
replicaset.apps/acmeair-flight-db-55dd58dbc9            1         1         1       26s
replicaset.apps/acmeair-flight-deployment-69b776b47f    1         1         1       26s
replicaset.apps/acmeair-main-deployment-646875f86d      1         1         1       27s


```


* Run the ingress-controller 

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/baremetal/deploy.yaml
```

* get the NodePort for ingress-controller 

```
controlplane $ kubectl get svc ingress-nginx-controller -n ingress-nginx
NAME                       TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   NodePort   10.98.45.73   <none>        80:32592/TCP,443:30488/TCP   4m8s
```


* Access the Machine URL on port 32592 and add /acmeair to the URL

```
  http://ip172-18-0-11-bsvevsuj2b7000f9euj0.direct.labs.play-with-k8s.com:32592/acmeair

  https://2886795298-32592-cykoria04.environments.katacoda.com/acmeair

```
