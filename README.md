# kubernetes-deployment-node-affinity

This repository demonstrates how to configure node affinity in Kubernetes using YAML manifests with separate and combined files.


## Purpose
This project is designed to help developers and DevOps engineers:
- Understand how node affinity works in Kubernetes
- Learn to modularize YAML files for better reuse
- Deploy sample apps to nodes matching specific labels

## Prerequisites

1. A running Kubernetes cluster (minikube, GKE, EKS, etc.)
2. kubectl command-line tool
3. Docker Hub (or any container registry) to store your app image
   
   Ensure your Minikube cluster is running:
    
       minikube start


## Steps:

**1. Clone the Repository.**
         
         git clone <repository_url>
         cd <repository_name>

**2. First you need to label or taint the node:**
        
         kubectl label nodes minikube disktype=ssd        
         kubectl taint nodes minikube special=true:NoSchedule

**3. Then you need to apply the combined manifest file :**
       
        kubectl apply -f combined-manifest.yaml

**4. Then you can check pods and their node placement:**
    
        kubectl get pods -o wide

## How Node Affinity Works in This Project 

**1. node-affinity.yaml file**

Node affinity rules are defined within the Pod spec to control where the pod can be scheduled, based on node labels and existing pod locations.

Two types of affinity rules are used:

- Hard Rule (requiredDuringSchedulingIgnoredDuringExecution)
    This rule must be satisfied for the pod to be scheduled. In this case, the rule ensures:
    The pod will only be scheduled on nodes that have the label disktype=ssd.

- Soft Rule (preferredDuringSchedulingIgnoredDuringExecution)
   This rule is a preference, not a strict requirement.It instructs the scheduler to prefer nodes where the following condition is met:
    The node already hosts a pod with the label app=my-app, within the same topology domain (i.e., same kubernetes.io/hostname).
    This helps group related pods together, improving locality and potentially performance.

**2. Combined the Manifests**

The combined-manifest.yaml merges the Deployment configuration with:
- Node affinity rules
- Tolerations (to allow scheduling on tainted nodes)
- Resource requests and limits
- By using a combined file:
   All configuration can be applied in a single step, making it easier to deploy and manage.

