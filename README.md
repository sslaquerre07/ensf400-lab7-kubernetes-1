# ensf400-lab7-kubernetes-1

## Objectives
This lab will teach us the basic usage of Kubernetes. Through Minikube, a simplified Kubernetes engine running on a single computer, we practice the key concepts and usage of Kubernetes.

## Environment

### Set Up Your GitHub CodeSpaces Instance

Same as Lab 6, this lab will be performed in [GitHub CodeSpaces](https://github.com/codespaces). Create an instance using GitHub Codespaces. Choose repository `denoslab/ensf400-lab7-kubernetes-1`.


```bash
minikube start
```

This step will start the Minikube service in a container.

## Steps

Go to Section 1 - 6 and complete the steps for each section. The steps can be found in the `README.md` files in each subdirectory.

## Have Your Work Checked By a TA

The TA will check the completion of the following tasks:

- Output of Section 1.
- Output of Section 2.
- Output of Section 3.
- Output of Section 4.
- Output of Section 5.
- Output of Section 6.

Each member of the group should be able to answer all of the following questions. The TA will ask each person one question selected at random, and the student must be able to answer the question to get credit for the lab.

- Q1: How to check which Node a Pod is running on? Use an example to show where this information can be found.
- A: kubectl get pods -o wide
- Execution Steps:
    - Start Pod from pods01.yaml: 
        - cd /workspaces/ensf400-lab7-kubernetes-1/1-pods
        - kubectl apply -f pods01.yaml
    - Retrive the node the pod has started on: kubectl get pods -o wide
    - Finally delete the node: kubectl delete -f pods01.yaml
<br />
- Q2: How to scale a ReplicaSet? Use an example to demonstrate scaling a ReplicaSet to 3 replicas.
- A: A simple command, for the class example is: kubectl scale --replicas=3 -f nginx_replicaset.yaml
- Execution Steps:
    - Start the ReplicaSet:
        - cd /workspaces/ensf400-lab7-kubernetes-1/2-replicasets
        - kubectl apply -f nginx_replicaset.yaml
    - Scale the # of pods: kubectl scale --replicas=3 -f nginx_replicaset.yaml
    - Delete the ReplicaSet: kubectl delete -f nginx_replicaset.yaml
<br />
- Q3: Briefly describe the process of a deployment rolling update, i.e., how are new version of pods created and how are old version of pods terminated. Which configurations can control the pods being created or deleted in parallel?
- Q4: Inside the Kubernetes cluster, how to access a service named "svc1" offering HTTP service at Port 8000?
- Q5: What is an Ingress in Kubernetes? What type of resources does an Ingress configuration typically point to as its backend? 
