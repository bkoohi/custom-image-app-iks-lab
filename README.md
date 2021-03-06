# custom-image-app-iks-lab
Deploy an App using a custom image in IKS cluster Workshop

The purpose of this structured, no-cost, hands-on, instructor-led learning experience is to demonstrate how to combine different technologies such as Container Orchestration Platform, Container Registry, and cloud computing environment to kickstart cloud native applications. Specifically, the attendees will learn how to:

 Deploy a Hello world custom image application in the Kubernetes cluster.
 Bind to default subdomain.

Additional IBM Cloud solution tutotials can be access from this link: https://cloud.ibm.com/docs/solution-tutorials?topic=solution-tutorials-scalable-webapp-kubernetes

## 2- Lab preparation
Follow these instructions for the setup of the initial Lab environment.

1- Create an access group in your IBM Cloud account: "Demo LABs"
https://cloud.ibm.com/iam/groups
Create
Enter "Demo LABs" as name of access group
Enter a short description
Add the following access polices:
	
1.1 VPC Infrastructure Services service
1.2 Cloud Object Storage service
1.3 Security Advisor service
1.4 All resource group
    resourceType string equals resource-group
1.5 Toolchain service
1.6 Certificate Manager service
1.7 Schematics service
1.8 All resources in account (including future IAM enabled services)

2- Create a resource group "labs"
https://cloud.ibm.com/account/resource-groups

3- Invite users to "Demo LABs" access group via:
https://cloud.ibm.com/iam/users/invite_users
enter user's email address
select "Demo LABs"

Users need to go to their notification section to accept the invite:
https://cloud.ibm.com/notifications
First time users of ibm cloud need to create a new ibm cloud id. Consult with your ibm contact for first time ID creation ( https://cloud.ibm.com/registration ) 

4- Work with your IKS team to deploy an IKS cluster required for this lab.

## 3- Objectives

Deploy a custom image application to the Kubernetes cluster.

3.1- Download and prepare a custom image using Quick start for Container registery ( https://cloud.ibm.com/registry/start )

3.2- build the application to produce a container image

3.3- Push image to IBM Cloud Container Registry

3.4- Deploy application to a Kubernetes cluster


## 4- Start a new IBM Cloud Shell or setup your client
You can use IBM Cloud Shell or setup your laptop client environment.

4.1 From the IBM Cloud console in your browser, select the account where you have been invited.

4.2 Click the button in the upper right corner to create a new Cloud Shell ( https://cloud.ibm.com/shell )

4.3 If you are interested to execute this lap from your laptop , you need to install the following tools in your laptop:
Install must-have tools to be productive with IBM Cloud:

    * IBM Cloud CLI - the command line interface to interact with IBM Cloud API.
    * Docker - to deliver and run software in packages called containers.
    * kubectl - a command line interface for running commands against Kubernetes clusters.
    * oc - manages OpenShift applications, and provides tools to interact with each component of your system.
    * Helm 3 - helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.
    * Terraform - automates your resource provisioning.
    * jq - a lightweight and flexible command-line JSON processor.
    * Git - a free and open source distributed version control system.

For tools installation, please follow instructions in this link: https://cloud.ibm.com/docs/solution-tutorials?topic=solution-tutorials-tutorials


### 5- Install, Set Up, and Log In

1. Start a shell in your local laptop/jump VM

2. If IBM Cloud CLIs are not installed in your laptop/jump VM then install the IBM Cloud CLI: https://cloud.ibm.com/docs/containers?topic=containers-cs_cli_install

4. If Docker/Podman not installed in your laptop/jump VM then install the Docker CLI: https://docs.docker.com/engine/install/

5. Install the Container Registry plug-in.
```
ibmcloud plugin install container-registry -r 'IBM Cloud'
```
6. Log in to your IBM Cloud account.
```
ibmcloud login -a https://cloud.ibm.com --sso
```
8. Ensure that you're targeting the correct IBM Cloud Container Registry region:

```
ibmcloud cr region-set global
```
9. Choose a name for your first namespace, and create that namespace. Use this namespace for the rest of the Quick Start.
```
ibmcloud cr namespace-add <your_initial>-hello-world
```

### 6- Push the image to your private registry

1. Log your local Docker daemon into the IBM Cloud Container Registry. Ensure your Docker or Podman is running in your env.
```
ibmcloud cr login
```
2.  Pull a test image from Docker Hub.
```
podman pull hello-world
docker pull hello-world
```

Output:
```
podman pull hello-world
Trying to pull docker.io/library/hello-world:latest...
Getting image source signatures
Copying blob sha256:2db29710123e3e53a794f2694094b9b4338aa9ee5c40b930cb8063a1be392c54
Copying config sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
Writing manifest to image destination
Storing signatures
feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
```
4. Choose a repository and tag by which you can identify the image. Use the same repository and tag for the rest of this Quick Start.
```
docker tag hello-world icr.io/bk-hello-world/hello-world:V1
podman tag hello-world icr.io/bk-hello-world/hello-world:V1
```
5- List images to validate the tag:
```
podman images
docker images
```

6- Push the image.
```
podman push icr.io/bk-hello-world/hello-world:V1
```

7- Verify that your image is in your private registry.
```
ibmcloud cr image-list
```

Output:
```
Listing images...

Repository                            Tag      Digest         Namespace           Created        Size     Security status   
icr.io/bk-hello-world/hello-world     V1       239de6dd745e   bk-hello-world      7 months ago   2.6 kB   Unsupported OS   
icr.io/code-engine-space/mean-stack   latest   25ace32e10a5   code-engine-space   3 weeks ago    52 MB    No Issues   

OK
```

### 7- Deploy hello-world from Container Registery in IKS clusrter ( to-be-completed )

1- Identify your cluster:
```
ibmcloud ks cluster ls
```

2- Initialize the variable with the cluster ID
```
export MYCLUSTER=<CLUSTER_ID>
```

3- Initialize the kubectl cli environment
```
ibmcloud ks cluster config --cluster $MYCLUSTER
```

4- If you wish to use the default Kubernetes namespace, run the below command to set an environment variable
```
export KUBERNETES_NAMESPACE=default
```

6- Install the hello-world app for IBM Cloud Registery:

```
kubectl create deployment helloworld --image=icr.io/bk-hello-world/hello-world
```
7- Display hello-world app deployment:
```
kubectl get deployments helloworld
kubectl describe deployments helloworld
```

8- Display hello-world ReplicaSet objects:
```
kubectl get replicasets
kubectl describe replicasets
```

9- Deploy a Kubernetes service for hello-world app: 
```
 kubectl expose deployment helloworld --type=LoadBalancer --name=helloworld --port 3000
```

7.2 Display Kubernetes services deployed in previous step:
```
kubectl describe service helloworld
```
It may take up to 5 min to update Loadbalancer in IBM Cloud with new service. As an example of output:
```
Name:                     helloworld
Namespace:                default
Labels:                   app=helloworld
Annotations:              <none>
Selector:                 app=helloworld
Type:                     LoadBalancer
IP Families:              <none>
IP:                       172.21.18.237
IPs:                      172.21.18.237
LoadBalancer Ingress:     a1c85bf6-us-south.lb.appdomain.cloud
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  31190/TCP
Endpoints:                
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                           Age    From                Message
  ----    ------                           ----   ----                -------
  Normal  EnsuringLoadBalancer             7m38s  service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer              7m15s  service-controller  Ensured load balancer
  Normal  CloudVPCLoadBalancerNormalEvent  3m11s  ibm-cloud-provider  Event on cloud load balancer helloworld for service default/helloworld with UID d93214ac-3db6-492e-a260-a29c49e2cd8b: The VPC load balancer that routes requests to this Kubernetes LoadBalancer service is currently online/active.
```
7.5 Use the loadbalancer in "LoadBalancer Ingress:" field and HTTP port in "TargetPort:". Open your application in a browser at https://"LoadBalancer Ingress:"TargetPort:". Output display should be:
```
Hello Kubernetes!
```

### 8-  Clean up and remove application
	
8.1 List application deployment
```
kubectl get deployments
```
	
8.2 Identify kubernetesnodeapp-deployment application in the list and delete
```
kubectl delete -n default deployment hello-world 
```
	
8.3 List installed ingress controller 
```
kubectl get ingress
```
	
8.4 Delete ngress-for-ibmdomain-http-and-https Ingress controller 
```
kubectl delete -n default service my-service  
```
	
