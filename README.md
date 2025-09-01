1-
kubectl run my-nginx --image=nginx --restart=Never
kubectl get pod my-nginx -o wide
2-
kubectl run my-nginx --image=nginx123 --restart=Never
kubectl get pod my-nginx
3-
kubectl describe pod my-nginx
kubectl logs my-nginx
4-
kubectl get pod my-nginx -o jsonpath='{.spec.nodeName}{"\n"}{.status.podIP}{"\n"}{.spec.containers[0].image}{"\n"}'

minikube-m02
10.244.1.4
nginx123

-5
kubectl delete pod my-nginx
kubectl get pods
6-
mkdir -p ~/k8s-task/{manifests,screenshots}
cd ~/k8s-task
kubectl apply -f manifests/pod-nginx.yaml
kubectl get pod my-nginx --show-labels -o wide
7-
nano manifests/rs-nginx.yaml

kubectl apply -f manifests/rs-nginx.yaml
kubectl get rs rs-nginx
kubectl get pods -l app=web -o wide


8-
kubectl scale rs rs-nginx --replicas=5
kubectl get pods -l app=web -o wide

9-
ReplicaSet ensures that the desired number of Pods is always running.
If you delete one Pod, the ReplicaSet will automatically create a new one to maintain the replica count

kubectl get pods -l app=web
kubectl delete pod rs-nginx-gxk4k
kubectl get pods -l app=web -w

10-
kubectl patch rs rs-nginx -p '{"spec":{"replicas":2}}'
kubectl get rs rs-nginx
kubectl get pods -l app=web

11-
ReplicaSet will not create Pods if the selector labels don’t match the template labels
selector is looking for tier=frontend but the Pods are created with tier=nginx

12- 
deployment (all lowercase), which is invalid, Kubernetes is case-sensitive . "Deployment" should be capitalized

13-
 the issue belongs to apiVersion Deployment is not part of apiVersion: v1. It belongs to the apps/v1 API group.

14- 
kubectl describe deployment [name of the deployment] | grep -i Image

15-
kubectl create deployment httpd-frontend --image=httpd:2.4-alpine --replicas=3 Output: deployment.apps/httpd-frontend created explanation: Created a deployment within the specs inside the command

16- 
kubectl set image deployment/httpd-frontend httpd=nginx777 Output: deployment.apps/httpd-frontend image updated explanation: Since nginx777 does not exist, pods will go into ImagePullBackOff.

17-
kubectl rollout undo deployment/httpd-frontend Output: deployment.apps/httpd-frontend rolled back explanation: All the pods are working with the latest image

18:21 - simple-wep directory

22-
kubectl apply -f backend-deployment.yaml
kubectl expose deployment backend-deployment --port=8080 --target-port=8080 --name=backend-service
kubectl port-forward service/backend-service 8080:8080
minikube service backend-service
images are provided in simple-wep/photos directory

23-
kubectl expose deployment frontend-deployment --type=LoadBalancer --port=80 --target-port=80 --name=frontend-lb
EXTERNAL-IP will remain <pending> forever.
The service exists but cannot be accessed externally.
To access it, you’d use:
minikube service frontend-lb --url (Minikube)
Or change type: LoadBalancer → NodePort.

24-
 A DaemonSet ensures that a copy of a Pod runs on every node in the cluster.
Commonly used for:
Log collection (e.g., Fluentd, Filebeat)
Monitoring agents (e.g., Prometheus node exporter)
Security agents


25-
he Master Node is responsible for managing the entire Kubernetes cluster. Its main components:
1-API Server (kube-apiserver)
The main entry point to Kubernetes.
All kubectl commands go through it.
Validates requests and updates the cluster state.
2- etcd
A key-value database that stores all cluster data.
Stores information about Pods, Services, configurations, and the cluster state.
3-Controller Manager (kube-controller-manager)
Runs controllers that maintain the desired state of the cluster.
Examples: Node Controller, Replication Controller, Endpoint Controller.
4-Scheduler (kube-scheduler)
Decides which Node a new Pod will run on.
Considers available resources like CPU, memory, and constraints.
5- Cloud Controller Manager (for cloud environments)
 Communicates with the cloud provider to manage resources like Load Balancers, volumes, and networking.

26- Components of the Worker Node
The Worker Node runs application workloads (Pods) and communicates with the Master Node. Its main components:
1-Kubelet
An agent that runs on each Worker Node.
Ensures the containers in the Pods are running as expected.
2-Kube-Proxy
Maintains network rules on nodes.
Enables communication to Pods from inside or outside the cluster.
3-Container Runtime
The software responsible for running containers.
Examples: Docker, containerd, or CRI-O.
