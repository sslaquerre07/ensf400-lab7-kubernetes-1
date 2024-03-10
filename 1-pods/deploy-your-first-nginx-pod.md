# Deploying Your First Nginx Pod

## What are Kubernetess Pods?


- Kubernetes pods are the foundational unit for all higher Kubernetes objects.
- A pod hosts one or more containers.
- It can be created using either a command or a YAML/JSON file.
- Use `kubectl` to create pods, view the running ones, modify their configuration, or terminate them. Kuberbetes will attempt to restart a failing pod by default.
- If the pod fails to start indefinitely, we can use the `kubectl describe` command to know what went wrong.

## Why does Kubernetes use a Pod as the smallest deployable unit, and not a single container?

While it would seem simpler to just deploy a single container directly, there are good reasons to add a layer of abstraction represented by the Pod. A container is an existing entity, which refers to a specific thing. That specific thing might be a Docker container, but it might also be a [rkt](https://coreos.com/rkt/) container, or a VM managed by Virtlet. Each of these has different requirements.

What’s more, to manage a container, Kubernetes needs additional information, such as a restart policy, which defines what to do with a container when it terminates, or a liveness probe, which defines an action to detect if a process in a container is still alive from the application’s perspective, such as a web server responding to HTTP requests.

Instead of overloading the existing “thing” with additional properties, Kubernetes architects have decided to use a new entity, the Pod, that logically contains (wraps) one or more containers that should be managed as a single entity.

## Why does Kubernetes allow more than one container in a Pod?

Containers in a Pod run on a “logical host”; they use the same network namespace (in other words, the same IP address and port space), and the same [IPC](https://en.wikipedia.org/wiki/Inter-process_communication) namespace. They can also use shared volumes. These properties make it possible for these containers to efficiently communicate, ensuring data locality. Also, Pods enable you to manage several tightly coupled application containers as a single unit.

So if an application needs several containers running on the same host, why not just make a single container with everything you need? Well first, you’re likely to violate the “one process per container” principle. This is important because with multiple processes in the same container it is harder to troubleshoot the container. That is because logs from different processes will be mixed together and it is harder manage the processes lifecycle. For example to take care of “zombie” processes when their parent process dies. Second, using several containers for an application is simpler, more transparent, and enables decoupling software dependencies. Also, more granular containers can be reused between teams.


## A Typical Pod creation Workflow

<img width="891" alt="image" src="https://github.com/collabnix/kubelabs/assets/34368930/458ca251-8d23-4a9b-a48d-e3172d7236c6">


The workflow for creating a Pod in Kubernetes typically involves the following steps:

- Create a Pod Manifest: A Pod is defined using a YAML or JSON manifest file that describes its desired state. The manifest includes information such as the Pod name, container specifications, networking details, and any additional configurations.
- Apply the Manifest: Use the kubectl apply command to apply the Pod manifest and create the Pod. For example:
```
kubectl apply -f pod.yaml
```

- API Server Validation: The kubectl apply command sends the Pod manifest to the Kubernetes API server. The API server validates the manifest's syntax and checks for any conflicts or errors.
- Pod Scheduler: Once the Pod manifest is validated, the Kubernetes scheduler assigns the Pod to a suitable worker node. The scheduler takes into account factors such as resource availability, node affinity rules, and other scheduling constraints.
- Container Creation: The assigned worker node receives the Pod specification and initiates the creation of containers within the Pod. The container runtime, such as Docker or containerd, pulls the container images specified in the Pod manifest and starts the containers.
- Pod Status: The Pod goes through different status phases, including "Pending" while it's being scheduled, "Running" when the containers are successfully started, and "Completed" or "Failed" when the Pod's primary container finishes its execution.
- Monitoring and Logging: Kubernetes provides various monitoring and logging mechanisms to track the status, resource usage, and events related to the Pod. You can use tools like Prometheus, Grafana, or Kubernetes Dashboard to monitor and visualize Pod metrics.

## Steps

```
cd 1-pods
kubectl apply -f pods01.yaml
```

## Viewing Your Pods

```
kubectl get pods
```

## Which Node Is This Pod Running On?

```
kubectl get pods -o wide
```

```
Name:             webserver
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sat, 09 Mar 2024 23:34:13 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:  10.244.0.3
Containers:
  webserver:
    Container ID:   docker://1283296c335704c7d4af23a1b06fcca7641b0b540ba53ecf67a2e5e3cc980099
    Image:          nginx:latest
    Image ID:       docker-pullable://nginx@sha256:c26ae7472d624ba1fafd296e73cecc4f93f853088e6a9c13c0d52f6ca5865107
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 09 Mar 2024 23:34:21 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-h92qn (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-h92qn:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  55s   default-scheduler  Successfully assigned default/webserver to minikube
  Normal  Pulling    55s   kubelet            Pulling image "nginx:latest"
  Normal  Pulled     48s   kubelet            Successfully pulled image "nginx:latest" in 7.113s (7.113s including waiting)
  Normal  Created    47s   kubelet            Created container webserver
  Normal  Started    47s   kubelet            Started container webserver
 ```
  
## Output in JSON
 
```json
$ kubectl get pods -o json
{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "v1",
            "kind": "Pod",
            "metadata": {
                "annotations": {
                    "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"name\":\"webserver\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"image\":\"nginx:latest\",\"name\":\"webserver\",\"ports\":[{\"containerPort\":80}]}]}}\n"
                },
                "creationTimestamp": "2024-03-09T23:34:12Z",
                "name": "webserver",
                "namespace": "default",
                "resourceVersion": "1508",
                "uid": "36e891ef-2e3a-46a8-a798-09ea85f203c1"
            },
            "spec": {
                "containers": [
                    {
                        "image": "nginx:latest",
                        "imagePullPolicy": "Always",
                        "name": "webserver",
                        "ports": [
                            {
                                "containerPort": 80,
                                "protocol": "TCP"
                            }
                        ],
                        "resources": {},
                        "terminationMessagePath": "/dev/termination-log",
                        "terminationMessagePolicy": "File",
                        "volumeMounts": [
                            {
                                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                                "name": "kube-api-access-h92qn",
                                "readOnly": true
                            }
                        ]
                    }
                ],
                "dnsPolicy": "ClusterFirst",
                "enableServiceLinks": true,
                "nodeName": "minikube",
                "preemptionPolicy": "PreemptLowerPriority",
                "priority": 0,
                "restartPolicy": "Always",
                "schedulerName": "default-scheduler",
                "securityContext": {},
                "serviceAccount": "default",
                "serviceAccountName": "default",
                "terminationGracePeriodSeconds": 30,
                "tolerations": [
                    {
                        "effect": "NoExecute",
                        "key": "node.kubernetes.io/not-ready",
                        "operator": "Exists",
                        "tolerationSeconds": 300
                    },
                    {
                        "effect": "NoExecute",
                        "key": "node.kubernetes.io/unreachable",
                        "operator": "Exists",
                        "tolerationSeconds": 300
                    }
                ],
                "volumes": [
                    {
                        "name": "kube-api-access-h92qn",
                        "projected": {
                            "defaultMode": 420,
                            "sources": [
                                {
                                    "serviceAccountToken": {
                                        "expirationSeconds": 3607,
                                        "path": "token"
                                    }
                                },
                                {
                                    "configMap": {
                                        "items": [
                                            {
                                                "key": "ca.crt",
                                                "path": "ca.crt"
                                            }
                                        ],
                                        "name": "kube-root-ca.crt"
                                    }
                                },
                                {
                                    "downwardAPI": {
                                        "items": [
                                            {
                                                "fieldRef": {
                                                    "apiVersion": "v1",
                                                    "fieldPath": "metadata.namespace"
                                                },
                                                "path": "namespace"
                                            }
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                ]
            },
            "status": {
                "conditions": [
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2024-03-09T23:34:13Z",
                        "status": "True",
                        "type": "Initialized"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2024-03-09T23:34:21Z",
                        "status": "True",
                        "type": "Ready"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2024-03-09T23:34:21Z",
                        "status": "True",
                        "type": "ContainersReady"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2024-03-09T23:34:12Z",
                        "status": "True",
                        "type": "PodScheduled"
                    }
                ],
                "containerStatuses": [
                    {
                        "containerID": "docker://1283296c335704c7d4af23a1b06fcca7641b0b540ba53ecf67a2e5e3cc980099",
                        "image": "nginx:latest",
                        "imageID": "docker-pullable://nginx@sha256:c26ae7472d624ba1fafd296e73cecc4f93f853088e6a9c13c0d52f6ca5865107",
                        "lastState": {},
                        "name": "webserver",
                        "ready": true,
                        "restartCount": 0,
                        "started": true,
                        "state": {
                            "running": {
                                "startedAt": "2024-03-09T23:34:21Z"
                            }
                        }
                    }
                ],
                "hostIP": "192.168.49.2",
                "phase": "Running",
                "podIP": "10.244.0.3",
                "podIPs": [
                    {
                        "ip": "10.244.0.3"
                    }
                ],
                "qosClass": "BestEffort",
                "startTime": "2024-03-09T23:34:13Z"
            }
        }
    ],
    "kind": "List",
    "metadata": {
        "resourceVersion": ""
    }
}            
 ```
 


## Executing Commands Against Pods


```bash
$ kubectl exec -it webserver -- /bin/bash
root@webserver:/#
```

```bash
root@webserver:/# cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
NAME="Debian GNU/Linux"
VERSION_ID="12"
VERSION="12 (bookworm)"
VERSION_CODENAME=bookworm
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

Please exit from the shell (`/bin/bash`) session.

```bash
root@webserver:/# exit
```


## Deleting the Pod
  
```bash
$ kubectl delete -f pods01.yaml
pod "webserver" deleted

$ kubectl get po -o wide
No resources found.
```

## Get logs of Pod

```
$ kubectl logs webserver

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/03/09 23:34:21 [notice] 1#1: using the "epoll" event method
2024/03/09 23:34:21 [notice] 1#1: nginx/1.25.4
2024/03/09 23:34:21 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/03/09 23:34:21 [notice] 1#1: OS: Linux 6.2.0-1019-azure
2024/03/09 23:34:21 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/03/09 23:34:21 [notice] 1#1: start worker processes
2024/03/09 23:34:21 [notice] 1#1: start worker process 29
2024/03/09 23:34:21 [notice] 1#1: start worker process 30

```
# Adding a 2nd container to a Pod

In the microservices architecture, each module should live in its own space and communicate with other modules following a set of rules. But, sometimes we need to deviate a little from this principle. Suppose you have an Nginx web server running and we need to analyze its web logs in real-time. The logs we need to parse are obtained from GET requests to the web server. The developers created a log watcher application that will do this job and they built a container for it. In typical conditions, you’d have a pod for Nginx and another for the log watcher. However, we need to eliminate any network latency so that the watcher can analyze logs the moment they are available. A solution for this is to place both containers on the same pod.

Having both containers on the same pod allows them to communicate through the loopback interface (`ifconfig lo`) as if they were two processes running on the same host. They also share the same storage volume.


Let us see how a pod can host more than one container. Let’s take a look to the [`pods02.yaml`](pods02.yaml) file. It contains the following lines:

```
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
  - name: webserver
    image: nginx:latest
    ports:
    - containerPort: 80
  - name: webwatcher
    image: afakharany/watcher:latest
```

Run the following command:

```
$ kubectl apply -f pods02.yaml
```



```
$ kubectl get po -o wide
NAME        READY   STATUS              RESTARTS   AGE   IP       NODE                                                NOMINATED NODE   READINESS GATES
webserver   0/2     ContainerCreating   0          13s   <none>   gke-standard-cluster-1-default-pool-78257330-5hs8   <none>           <none>
 ```
 
 ```
$ kubectl get po,svc,deploy
NAME            READY   STATUS    RESTARTS   AGE
pod/webserver   2/2     Running   0          3m6s
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.12.0.1    <none>        443/TCP   107m
```


```
$ kubectl get po -o wide
NAME        READY   STATUS    RESTARTS   AGE     IP         NODE                                                NOMINATED NODE   READINESS GATES
webserver   2/2     Running   0          3m37s   10.8.0.5   gke-standard-cluster-1-default-pool-78257330-5hs8   <none>           <none>
```



## How to verify 2 containers are running inside a Pod?


```
$ kubectl describe po
```

```
Containers:
  webserver:
    Container ID:   docker://0564fcb88f7c329610e7da24cba9de6555c0183814cf517e55d2816c6539b829
    Image:          nginx:latest
    Image ID:       docker-pullable://nginx@sha256:36b77d8bb27ffca25c7f6f53cadd059aca2747d46fb6ef34064e31727325784e
    Port:           80/TCP
    State:          Running
      Started:      Wed, 08 Jan 2020 13:21:57 +0530
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-xhgmm (ro)
  webwatcher:
    Container ID:   docker://4cebbb220f7f9695f4d6492509e58152ba661f3ab8f4b5d0a7adec6c61bdde26
    Image:          afakharany/watcher:latest
    Image ID:       docker-pullable://afakharany/watcher@sha256:43d1b12bb4ce6e549e85447678a28a8e7b9d4fc398938a6f3e57d2908a9b7d80
    Port:           <none>
    State:          Running
      Started:      Wed, 08 Jan 2020 13:22:26 +0530
    Ready:          True
    Restart Count:  0
    Requests:
 ```

Since we have two containers in a pod, we will need to use the `-c` option with `kubectl` when we need to address a specific container. For example:

```
$ kubectl exec -it webserver -c webwatcher -- /bin/bash

root@webserver:/# cat /etc/hosts
# Kubernetes-managed hosts file.
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
fe00::0 ip6-mcastprefix
fe00::1 ip6-allnodes
fe00::2 ip6-allrouters
10.8.0.5        webserver
```

Please exit from the shell (`/bin/bash`) session.

```
root@webserver:/# exit
```

## Cleaning up

```
kubectl delete -f pods02.yaml
```

# Example of Multi-Container Pod

Let's talk about communication between containers in a Pod. Having multiple containers in a single Pod makes it relatively straightforward for them to communicate with each other. They can do this using several different methods.

## Use Cases for Multi-Container Pods

The primary purpose of a multi-container Pod is to support co-located, co-managed helper processes for a primary application. There are some general patterns for using helper processes in Pods:

*Sidecar containers* help the main container. Some examples include log or data change watchers, monitoring adapters, and so on. A log watcher, for example, can be built once by a different team and reused across different applications. Another example of a sidecar container is a file or data loader that generates data for the main container.

*Proxies, bridges, and adapters* connect the main container with the external world. For example, Apache HTTP server or nginx can serve static files. It can also act as a reverse proxy to a web application in the main container to log and limit HTTP requests. 
Another example is a helper container that re-routes requests from the main container to the external world. This makes it possible for the main container to connect to the localhost to access, for example, an external database, but without any service discovery.

## Shared volumes in a Kubernetes Pod

In Kubernetes, you can use a shared Kubernetes Volume as a simple and efficient way to share data between containers in a Pod. For most cases, it is sufficient to use a directory on the host that is shared with all containers within a Pod.

Kubernetes Volumes enables data to survive container restarts, but these volumes have the same lifetime as the Pod. That means that the volume (and the data it holds) exists exactly as long as that Pod exists. If that Pod is deleted for any reason, even if an identical replacement is created, the shared Volume is also destroyed and created anew.

A standard use case for a multi-container Pod with a shared Volume is when one container writes logs or other files to the shared directory, and the other container reads from the shared directory. For example, we can create a Pod like so ([pods03.yaml](pods03.yaml)):

```
apiVersion: v1
kind: Pod
metadata:
  name: mc1
spec:
  volumes:
  - name: html
    emptyDir: {}
  containers:
  - name: 1st
    image: nginx
    volumeMounts:
    - name: html
      mountPath: /usr/share/nginx/html
  - name: 2nd
    image: debian
    volumeMounts:
    - name: html
      mountPath: /html
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
          date >> /html/index.html;
          sleep 1;
        done
```

In this file (`pods03.yaml`) a volume named `html` has been defined. Its type is `emptyDir`, which means that the volume is first created when a Pod is assigned to a node, and exists as long as that Pod is running on that node. As the name says, it is initially empty. The `1st` container runs nginx server and has the shared volume mounted to the directory `/usr/share/nginx/html`. The `2nd` container uses the Debian image and has the shared volume mounted to the directory `/html`. Every second, the `2nd` container adds the current date and time into the `index.html` file, which is located in the shared volume. When the user makes an HTTP request to the Pod, the Nginx server reads this file and transfers it back to the user in response to the request.


![Image](https://raw.githubusercontent.com/collabnix/kubelabs/master/pods101/multicontainerpod.png)

```
kubectl apply -f pods03.yaml
```

```
[Captains-Bay]🚩 >  kubectl get po,svc
NAME      READY     STATUS    RESTARTS   AGE
po/mc1    2/2       Running   0          11s

NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
svc/kubernetes   ClusterIP   10.15.240.1   <none>        443/TCP   1h
[Captains-Bay]🚩 >  kubectl describe po mc1
Name:         mc1
Namespace:    default
Node:         gke-k8s-lab1-default-pool-fd9ef5ad-pc18/10.140.0.16
Start Time:   Wed, 08 Jan 2020 14:29:08 +0530
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"mc1","namespace":"default"},"spec":{"containers":[{"image":"nginx","name":"1st","v...
              kubernetes.io/limit-ranger=LimitRanger plugin set: cpu request for container 1st; cpu request for container 2nd
Status:       Running
IP:           10.12.2.6
Containers:
  1st:
    Container ID:   docker://b08eb646f90f981cd36c605bf8fead3ca62178c7863598fd4558cb026ed067dd
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:36b77d8bb27ffca25c7f6f53cadd059aca2747d46fb6ef34064e31727325784e
    Port:           <none>
    State:          Running
      Started:      Wed, 08 Jan 2020 14:29:09 +0530
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /usr/share/nginx/html from html (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-xhgmm (ro)
  2nd:
    Container ID:  docker://63180b4128d477810d6062342f4b8e499de684ffd69ad245c29118e1661eafcb
    Image:         debian
    Image ID:      docker-pullable://debian@sha256:c99ed5d068d4f7ff36c7a6f31810defebecca3a92267fefbe0e0cf2d9639115a
    Port:          <none>
    Command:
      /bin/sh
      -c
    Args:
      while true; do date >> /html/index.html; sleep 1; done
    State:          Running
      Started:      Wed, 08 Jan 2020 14:29:14 +0530
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /html from html (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-xhgmm (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  html:
    Type:    EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:  
  default-token-xhgmm:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-xhgmm
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From                                              Message
  ----    ------     ----  ----                                              -------
  Normal  Scheduled  18s   default-scheduler                                 Successfully assigned default/mc1 to gke-k8s-lab1-default-pool-fd9ef5ad-pc18
  Normal  Pulling    17s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  pulling image "nginx"
  Normal  Pulled     17s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Successfully pulled image "nginx"
  Normal  Created    17s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Created container
  Normal  Started    17s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Started container
  Normal  Pulling    17s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  pulling image "debian"
  Normal  Pulled     13s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Successfully pulled image "debian"
  Normal  Created    12s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Created container
  Normal  Started    12s   kubelet, gke-k8s-lab1-default-pool-fd9ef5ad-pc18  Started container
```



```
$ kubectl exec mc1 -c 1st -- /bin/cat /usr/share/nginx/html/index.html
...
Wed Jan  8 08:59:14 UTC 2020
Wed Jan  8 08:59:15 UTC 2020
Wed Jan  8 08:59:16 UTC 2020
 
$ kubectl exec mc1 -c 2nd -- /bin/cat /html/index.html
...
Wed Jan  8 08:59:14 UTC 2020
Wed Jan  8 08:59:15 UTC 2020
Wed Jan  8 08:59:16 UTC 2020
```

## Cleaning Up

```
kubectl delete -f pods03.yaml
```





