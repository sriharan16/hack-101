---
layout: post
title:  "Kubernetes Sample Commands"
---

# Imperative Approach Commands

## Kubeconfig

```bash
# Inspect  the endpoints for the cluster and installed add-ons 
kubectl cluster-info

# List all the nodes in the cluster. 
# To get a more detailed view of the nodes, the `-o wide` flag can be passed
kubectl get nodes [-o wide] 

# Describe a cluster node.
# Typical configuration: node IP, capacity (CPU and memory), a list of running pods on the node, podCIDR, etc.
kubectl describe node {{ NODE NAME }}
```
## Deployment

To create a deployment, use the kubectl create deployment command, with the following syntax:

```bash
# create a Deployment resource
# NAME - required; set the name of the deployment
# IMAGE - required;  specify the Docker image to be executed
# FLAGS - optional; provide extra configuration parameters for the resource
# COMMAND and args - optional; instruct the container to run specific commands when it starts 
kubectl create deploy NAME --image=image [FLAGS] -- [COMMAND] [args]

# Some of the widely used FLAGS are:
-r, --replicas - set the number of replicas
-n, --namespace - set the namespace to run
--port - expose the container port
```

For example, to create a Deployment for the Go [hello-world](https://hub.docker.com/repository/docker/sriharan16/go-helloworld) application, the following command can be used:

```bash
# create a go-helloworld Deployment in namespace `test`
kubectl create deploy go-helloworld --image=pixelpotato/go-helloworld:v1.0.0 -n test
```

To create a headless pod, the kubectl run command is handy, with the following syntax:

```bash
# create a headless pod
# NAME - required; set the name of the pod
# IMAGE - required;  specify the Docker image to be executed
# FLAGS - optional; provide extra configuration parameters for the resource
# COMMAND and args - optional; instruct the container to run specific commands when it starts 
kubectl run NAME --image=image [FLAGS] -- [COMMAND] [args...]

# Some of the widely used FLAGS are:
--restart - set the restart policy. Options [Always, OnFailure, Never]
--dry-run - dry run the command. Options [none, client, server]
-it - open an interactive shell to the container
```

For example, to create a busybox pod, the following command can be used:

```bash
# example: create a busybox pod, with an interactive shell and a restart policy set to Never 
kubectl run -it busybox-test --image=busybox --restart=Never
```

## Expose Deployment

To create a service for an existing deployment, use the kubectl expose deployment command, with the following syntax:

```bash
# expose a Deployment through a Service resource 
# NAME - required; set the name of the deployment to be exposed
# --port - required; specify the port that the service should serve on
# --target-port - optional; specify the port on the container that the service should direct traffic to
# FLAGS - optional; provide extra configuration parameters for the service
kubectl expose deploy NAME --port=port [--target-port=port] [FLAGS]

# Some of the widely used FLAGS are:
--protocol - set the network protocol. Options [TCP|UDP|SCTP]
--type - set the type of service. Options [ClusterIP, NodePort, LoadBalancer]
```

For example, to expose the Go hello-world application through a service, the following command can be used:

```bash
# expose the `go-helloworld` deployment on port 8111
# note: the application is serving requests on port 6112
kubectl expose deploy go-helloworld --port=8111 --target-port=6112
```

## ConfigMaps

To create a Configmap use the kubectl create configmap command, with the following syntax: 

```bash
# create a Configmap
# NAME - required; set the name of the configmap resource
# FLAGS - optional; define  extra configuration parameters for the configmap
kubectl create configmap NAME [FLAGS]

# Some of the widely used FLAGS are:
--from-file - set path to file with key-value pairs 
--from-literal - set key-value pair from command-line
```

For example, to create a Configmap to store the background color for a front-end application, the following command can be used: 

```bash
# create a Configmap to store the color value
kubectl create configmap test-cm --from-literal=color=yellow
```

## Secrets

To create a Secret use the kubectl create secret generic command, with the following syntax: 

```bash
# create a Secret
# NAME - required; set the name of the secret resource
# FLAGS - optional; define  extra configuration parameters for the secret
kubectl create secret generic NAME [FLAGS]

# Some of the widely used FLAGS are:
--from-file - set path to file with the sensitive key-value pairs 
--from-literal - set key-value pair from command-line 
```

For example, to create a Secret to store the secret background color for a front-end application, the following command can be used:

```bash
# create a Secret to store the secret color value
kubectl create secret generic test-secret --from-literal=color=blue
```

## Namespace

To create a Namespace we can use the kubectl create namespace command, with the following syntax:

```bash
# create a Namespace
# NAME - required; set the name of the Namespace
kubectl create ns NAME
```

For example, to create a test-udacity Namespace, the following command can be used:

```bash
# create a `test-udacity` Namespace
kubectl create ns test-udacity

# get all the pods in the `test-udacity` Namespace
kubectl get po -n test-udacity
```

## Summary

Kubectl provides a rich set of actions that can be used to interact, manage, and configure Kubernetes resources. Below is a list of handy kubectl commands used in practice.

Note: In the following commands the following arguments are used:

    RESOURCE is the Kubernetes resource type
    NAME sets the name of the resource
    FLAGS are used to provide extra configuration
    PARAMS are used to provide the required configuration to the resource

### Create Resources

To create resources, use the following command:

```bash
kubectl create RESOURCE NAME [FLAGS]
```

### Describe Resources

To describe resources, use the following command:

```bash
kubectl describe RESOURCE NAME 
```

### Get Resources

To get resources, use the following command, where -o yaml instructs that the result should be YAML formated.

```bash
kubectl get RESOURCE NAME [-o yaml]
```

### Edit Resources

To edit resources, use the following command, where -o yaml instructs that the edit should be YAML formated.

```bash
kubectl edit RESOURCE NAME [-o yaml]
```

### Label Resources

To label resources, use the following command:

```bash
kubectl label RESOURCE NAME [PARAMS]
```

### Port-forward to Resources

To access resources through port-forward, use the following command:

```bash
kubectl port-forward RESOURCE/NAME [PARAMS]
```

### Logs from Resources

To access logs from a resource, use the following command:

```bash
kubectl logs RESOURCE/NAME [FLAGS]
```

### Delete Resources

To delete resources, use the following command:

```bash
kubectl delete RESOURCE NAME
```

# Declarative Kubernetes Manifests

## YAML Manifest structure

A YAML manifest consists of 4 obligatory sections:

- apiversion - API version used to create a Kubernetes object
- kind - object type to be created or configured
- metadata - stores data that makes the object identifiable, such as its name, namespace, and labels
- spec - defines the desired configuration state of the resource

>To get the YAML manifest of any resource within the cluster, use the kubectl get command, associated with the -o yaml flag, which requests the output in YAML format. Additionally, to explore all configurable parameters for a resource it is highly recommended to reference the official [Kubernetes documentation](https://kubernetes.io/docs/home/).


## Deployment YAML manifest

In addition to the required sections of a YAML manifest, a Deployment resource covers the configuration of ReplicaSet, RollingOut strategy, and containers. Bellow is a full manifest of a Deployment explaining each parameter:

```bash
## Set the API endpoint used to create the Deployment resource.
apiVersion: apps/v1
## Define the type of the resource.
kind: Deployment
## Set the parameters that make the object identifiable, such as its name, namespace, and labels.
metadata:
  annotations:
  labels:
    app: go-helloworld
  name: go-helloworld
  namespace: default
## Define the desired configuration for the Deployment resource.
spec:
  ## Set the number of replicas.
  ## This will create a ReplicaSet that will manage 3 pods of the Go hello-world application.
  replicas: 3
  ## Identify the pods managed by this Deployment using the following selectors.
  ## In this case, all pods with the label `go-helloworld`.
  selector:
    matchLabels:
      app: go-helloworld
  ## Set the RollingOut strategy for the Deployment.
  ## For example, roll out only 25% of the new pods at a time.
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  ## Set the configuration for the pods.
  template:
    ## Define the identifiable metadata for the pods.
    ## For example, all pods should have the label `go-helloworld`
    metadata:
      labels:
        app: go-helloworld
    ## Define the desired state of the pod configuration.
    spec:
      containers:
        ## Set the image to be executed inside the container and image pull policy
        ## In this case, run the `go-helloworld` application in version v2.0.0 and
        ## only pull the image if it's not available on the current host.
      - image: pixelpotato/go-helloworld:v2.0.0
        imagePullPolicy: IfNotPresent
        name: go-helloworld
        ## Expose the port the container is listening on.
        ## For example, exposing the application port 6112 via TCP.
        ports:
        - containerPort: 6112
          protocol: TCP
        ## Define the rules for the liveness probes.
        ## For example, verify the application on the main route `/`,
        ## on application port 6112. If the application is not responsive, then the pod will be restarted automatically. 
        livenessProbe:
           httpGet:
             path: /
             port: 6112
        ## Define the rules for the readiness probes.
        ## For example, verify the application on the main route `/`,
        ## on application port 6112. If the application is responsive, then traffic will be sent to this pod.
        readinessProbe:
           httpGet:
             path: /
             port: 6112
        ## Set the resource requests and limits for an application.
        resources:
        ## The resource requests guarantees that the desired amount 
        ## CPU and memory is allocated for a pod. In this example, 
        ## the pod will be allocated with 64 Mebibytes and 250 miliCPUs.
          requests:
            memory: "64Mi"
            cpu: "250m"
        ## The resource limits ensure that the application is not consuming 
        ## more than the specified CPU and memory values. In this example, 
        ## the pod will not surpass 128 Mebibytes and 500 miliCPUs.
          limits:
            memory: "128Mi"
            cpu: "500m"
```

## Service YAML manifest

In addition to the required sections of a YAML manifest, a Service resource covers the configuration of service type and ports the service should configure. Bellow is a full manifest of a Service explaining each parameter:

```bash
## Set the API endpoint used to create the Service resource.
apiVersion: v1
## Define the type of the resource.
kind: Service
## Set the parameters that make the object identifiable, such as its name, namespace, and labels.
metadata:
  labels:
    app: go-helloworld
  name: go-helloworld
  namespace: default
## Define the desired configuration for the Service resource.
spec:
  ## Define the ports that the service should serve on. 
  ## For example, the service is exposed on port 8111, and
  ## directs the traffic to the pods on port 6112, using TCP.
  ports:
  - port: 8111
    protocol: TCP
    targetPort: 6112
  ## Identify the pods managed by this Service using the following selectors.
  ## In this case, all pods with the label `go-helloworld`.
  selector:
    app: go-helloworld
  ## Define the Service type, here set to ClusterIP.
  type: ClusterIP
```

### Useful command

- Kubernetes YAML manifests can be created using the kubectl apply command, with the following syntax:

```bash
# create a resource defined in the YAML manifests with the name manifest.yaml
kubectl apply -f manifest.yaml
```

- To delete a resource using a YAML manifest, the kubectl delete command, with the following syntax:

```bash
# delete a resource defined in the YAML manifests with the name manifest.yaml
kubectl delete -f manifest.yaml
```

Kubernetes documentation is the best place to explore the available parameters for YAML manifests. However, a support YAML template can be constructed using kubectl commands. This is possible by using the `--dry-run=client` and `-o yaml` flags which instructs that the command should be evaluated on the client-side only and output the result in YAML format.

```bash
# get YAML template for a resource 
kubectl create RESOURCE [REQUIRED FLAGS] --dry-run=client -o yaml
```

For example, to get the template for a Deployment resource, we need to use the create command, pass the required parameters, and associated with the --dry-run and -o yaml flags. This outputs the base template, which can be used further for more advanced configuration.

```bash
# get the base YAML templated for a demo Deployment running a nxing application
 kubectl create deploy demo --image=nginx --dry-run=client -o yaml
```
