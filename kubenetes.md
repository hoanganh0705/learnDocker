use minikube start --driver=docker to start a local Kubernetes cluster.
use minikube status to check the status of the cluster.
use minikube dashboard to open the Kubernetes dashboard in a web browser.
use kubectl to communicate with the Kubernetes cluster.
minikube is used for creating a local Kubernetes cluster for development and testing purposes.
We can use kubectl create deployment to create a deployment in Kubernetes. You can use kubectl create deployment --image=nginx to create a deployment with nginx image.
Use kubectl get deployment to get the list of deployments.
Use kubectl get pods to get the list of pods.
Use kubectl delete deployment <deployment-name> to delete a deployment.
If you want to create a deployment with local image, you need to upload the docker image to the docker registry
When you create a deployment, the control plane will automatically chose the most suitable node to run the pod.
We can indirectly create a service by exposing the deployment. For example, we can use kubectl expose deployment <deployment-name> --type=NodePort --port=80 to expose the deployment to the outside world.
Now to check the service, we can use kubectl get service to get the list of services.
Now we need to create external link by commanding minikube service <service-name> to open the service in a web browser.
If you want to run mutiple pods, you can use kubectl scale deployment <deployment-name> --replicas=3 to scale the deployment to 3 pods.

Kubernestes concepts:

- Cluster is a collection of nodes that run containerized applications managed by Kubernetes. It contains a control plane and worker nodes.
- Node is a worker machine in Kubernetes that runs pods. We have 2 kinds of node: control plane node and worker node. Worker node runs the applications in pods.
- Pod is the smallest deployable unit in Kubernetes that contains one or more containers. All containers in one mutual pod have the same network namespace and can communicate with each other, moreover they have the same IP, volume and share the same life cycle.
- Deployment is a Kubernetes object that manages a set of identical pods. It ensures that a specified number of pod replicas are running at any given time. Deployment can help us scale applications, rolling updates, rollback and manage updates.
- Service is a Kubernetes object that defines a logical set of pods and a policy by which to access them. It provides a stable IP address and DNS name for a set of pods, enabling communication between different parts of an application. There are 3 kinds of service: cluster ip, node port and load balancer. ClusterIP just used for interal environment of that cluster and can not be accessed from outside. Node Port is used for opening a port in a node, it can be accessed through the node's IP address and the assigned port number for example <NodeIP>:<NodePort> which is 192.168.49.2:30007 in real life. Load Balancer is mostly used on cloud services
  Talk about service, pod is already dead when the node is dead, so we need service to provide a stable IP address and DNS name for a set of pods, enabling communication between different parts of an application. And without service we can not access the application from outside.
  Service can group pods together and provide a shared stable IP address and DNS name for them.
