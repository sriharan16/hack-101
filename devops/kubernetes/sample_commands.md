---
layout: post
title:  "Kubernetes Sample Commands"
---

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
