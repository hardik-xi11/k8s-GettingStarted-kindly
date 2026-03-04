# DONT CLOSE TABS OR USE MY ACCOUNTS

  

# DONOT CLOSE IF YOU ARE READING THIS

  

  

## Install kind

  

```powershell

  

winget install Kubernetes.kind --source winget

  

```

  

  

#### Check installation by running the "kind" command

  

```powershell

  

kind

  

kind version

  

```

  

  

## Install kubectl

  

  

```powershell

  

winget install -e --id Kubernetes.kubectl --source winget

  

```

  

## Create the cluster using kind

  

```powershell

  

kind create cluster

  

```

  #### Check the cluster nodes by running commands using kubectl

  ```

  

kubectl get nodes

  

```

  ###  To Delete your cluster use:

```bash

  

kind delete cluster

```

  
  

# Run your first pod

  

Running an Nginx pod using the `kubectl run` command 

```bash

  

kubectl run nginx-demo --image=nginx:latest --port=80

```

  

Port forwarding the nginx pod to see on local system

```bash

  

kubectl port-forward pod/nginx-demo 8080:80

```

---

  

#### First create a wordpress manifest file and then apply the manifest to create a deployment.

```bash

  

kubectl apply -f .\wordpress-demo.yaml

```

  

Port forwarding the wordpress service to use it on local system

```bash

  

kubectl port-forward service/wordpress-service 8080:80  

```

Scale the deployment to 3 pod replicas instead of 1

  

```bash

  

kubectl scale deployment wordpress-deployment --replicas=3

```

  

---

  

*commands to check logs*

  

```bash

kubectl get events --watch

```

  
  

```bash

kubectl describe pod <pod-name>

```

  
  

```bash

kubectl get pods --watch

```

  
  
  

```bash

kubectl logs -f <pod-name>

```

  
  

---

# Create a cluster with multiple nodes by defining a k8s config

  

### first delete the old cluster to remove the k8s config

#### list all clusters

```bash

kind get clusters

```

#### delete the default cluster called "kind"

```bash

kind delete cluster

```

  

#### or delete a specific cluster by name

```bash

kind delete cluster -n k8s-demo-cluster

```

  
  

### define the k8s config and use the following command to create the cluster

  

```bash

kind create cluster --config kind-multi-node.yaml

```

---

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

### Uninstall

  

```powershell

  

winget uninstall --id Kubernetes.kubectl

  

```

  

```powershell

  

winget uninstall --id Kubernetes.kind

  

```

  
  
  
  
  


  
  
  
  
  
  
  
  

- -    NOTES    -  -
    

  
  

This is a perfect moment to pause and educate your audience. You just ran a command that created 19 different resources! If you just gloss over this, it looks like "magic." If you explain it, it looks like "engineering."

Here is how to break this down for your demo.

### 1. The Analogy: "The Hotel Receptionist"

Start by telling the audience:

*"We just deployed the Ingress Controller. Think of our Kubernetes cluster as a giant hotel.

- The Pods are the hotel rooms (where the guests/apps live).
    
- The Service is the hallway.
    
- But right now, the front door is locked. No one from the outside (the internet/my laptop) can find the right room.
    

The Ingress Controller is the Receptionist at the front desk. It stands at the door (Port 80), checks your key card (URL path), and directs you to the correct room."*

### 2. The Technical Breakdown (The "What")

Point to the long list of output (created) and group them into three simple categories for the audience:

A. The Permissions (RBAC)

"Look at all these ServiceAccount, Role, and RoleBinding lines. This is security. We are giving this Receptionist a special ID badge so it can talk to the Kubernetes API. It needs permission to read our Ingress rules to know which new apps we've deployed."

B. The Logic (ConfigMap & Class)

"The ConfigMap and IngressClass are the rulebooks. They tell the controller how to behave—for example, 'We are using NGINX, and here is how we handle timeouts'."

C. The Workload (Deployment & Service)

"Finally, the deployment.apps/ingress-nginx-controller. This is the actual Nginx server itself—the software that does the routing. And the service exposes it so it can receive traffic."

### 3. The "Why" (The Problem it Solves)

Explain the specific problem you are solving right now in this demo:

*"Why did we need this specific manifest?

Because we are running Kind (Kubernetes in Docker).

By default, Kind is isolated. This manifest installs a specialized version of Nginx that knows how to 'hook into' the host ports (80 and 443) that we opened on our Control Plane node earlier. Without this, my browser would hit localhost:80 and get a 'Connection Refused' error."*

### 4. The Status Check (Addressing "ContainerCreating")

Finally, address the ContainerCreating status you see in the terminal.

*"If we check the pod status, it says ContainerCreating.

This means the node is currently downloading the Nginx Docker image from the registry. Since this is the first time we've run it, it might take a minute.

We cannot proceed until this says Running."*

Action:

Run this command to wait for it visually:

PowerShell

kubectl get pods -n ingress-nginx -w

# 🧱 Final Architecture You Built

Browser (k8site.local:8080)

       ↓

Ingress Controller (NGINX)

       ↓

ClusterIP Service

       ↓

Deployment (3 replicas)

       ↓

Pods running your Docker image

---

# 🔹 STEP 1 — Create Multi-Node Kind Cluster

## Why we did this

In production, Kubernetes runs across multiple nodes.  
We want to demonstrate:

- Scheduling  
      
    
- Load balancing  
      
    
- High availability  
      
    
- Real orchestration  
      
    

Not just “running a container”.

---

## 1️⃣ kind-multi-node.yaml

kind: Cluster

apiVersion: kind.x-k8s.io/v1alpha4

name: k8s-demo-cluster

nodes:

 - role: control-plane

   kubeadmConfigPatches:

     - |

       kind: InitConfiguration

       nodeRegistration:

         kubeletExtraArgs:

           node-labels: "ingress-ready=true"

   extraPortMappings:

     - containerPort: 80

       hostPort: 8080

       protocol: TCP

 - role: worker

 - role: worker

 - role: worker

---

## 2️⃣ Create Cluster

kind create cluster --config kind-multi-node.yaml

---

## 🎤 What You Say During Demo

“I am creating a 4-node Kubernetes cluster locally using kind.  
One control plane manages scheduling and API access.  
Three worker nodes run workloads.  
I map host port 8080 to container port 80 so I can expose ingress traffic to my local machine.”

---

# 🔹 STEP 2 — Install NGINX Ingress Controller

## Why we need this

Ingress is just a routing rule.

The Ingress Controller is the actual reverse proxy that:

- Watches Ingress resources  
      
    
- Configures NGINX dynamically  
      
    
- Routes HTTP traffic to services  
      
    

Without a controller, Ingress does nothing.

---

## Install Controller

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

Wait until ready:

kubectl get pods -n ingress-nginx

---

## 🎤 Demo Explanation

“Ingress is not a load balancer itself.  
It’s just a Kubernetes API object.  
The NGINX Ingress Controller watches for those objects  
and dynamically configures NGINX as a reverse proxy.”

---

# 🔹 STEP 3 — Deploy Your Website (Deployment + Service)

You pushed your image:

hardik0005/k8site:v3

---

## k8site.yaml

apiVersion: apps/v1

kind: Deployment

metadata:

 name: k8site

spec:

 replicas: 3

 selector:

   matchLabels:

     app: k8site

 template:

   metadata:

     labels:

       app: k8site

   spec:

     containers:

     - name: k8site

       image: hardik0005/k8site:v3

       ports:

       - containerPort: 80

---

apiVersion: v1

kind: Service

metadata:

 name: k8site-service

spec:

 type: ClusterIP

 selector:

   app: k8site

 ports:

 - port: 80

   targetPort: 80

Apply:

kubectl apply -f k8site.yaml

---

## 🎯 What This Does

### Deployment

- Creates 3 identical pods  
      
    
- Ensures they stay running  
      
    
- Reschedules if a node fails  
      
    

### Service (ClusterIP)

- Creates stable internal IP  
      
    
- Load balances across pods  
      
    
- Decouples traffic from pod IPs  
      
    

---

## 🎤 Demo Explanation

“The Deployment ensures three replicas of my website are always running.  
The Service provides an internal load balancer so traffic is evenly distributed across pods.  
This decouples traffic routing from pod lifecycle.”

Show:

kubectl get pods -o wide

Point out they’re on different worker nodes.

---

# 🔹 STEP 4 — Create Ingress Resource

## k8site-ingress.yaml

apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

 name: k8site-ingress

spec:

 ingressClassName: nginx

 rules:

 - host: k8site.local

   http:

     paths:

     - path: /

       pathType: Prefix

       backend:

         service:

           name: k8site-service

           port:

             number: 80

Apply:

kubectl apply -f k8site-ingress.yaml

---

## Add hosts entry (Windows)

127.0.0.1 k8site.local

---

## Access

http://k8site.local:8080

---

# 🎯 What This Does

Ingress:

- Matches HTTP Host header  
      
    
- Routes request to service  
      
    
- Service forwards to pods  
      
    
- Pods serve HTML  
      
    

---

## 🎤 Demo Explanation

“Ingress performs host-based routing.  
When a request comes in for k8site.local,  
the controller forwards it to the k8site-service,  
which then load balances across the pods.”

---

# 🔥 Show It’s Really Load Balancing

Run:

kubectl logs -l app=k8site

Refresh browser multiple times.

Explain:

“Traffic is distributed across pods.  
If one node fails, traffic continues flowing.”

---

# 🧠 What You Built (Conceptually)

|   |   |
|---|---|
|Component|Role|
|Cluster|Orchestrator|
|Deployment|Ensures replicas|
|Pods|Run container|
|Service|Internal load balancer|
|Ingress|HTTP routing rule|
|Ingress Controller|Reverse proxy implementation|

---

# 🏆 Final Demo Flow Script

1. Create cluster  
      
    
2. Install ingress controller  
      
    
3. Deploy app (3 replicas)  
      
    
4. Show pods distributed  
      
    
5. Show service abstraction  
      
    
6. Create ingress  
      
    
7. Access via domain  
      
    
8. Explain host-based routing  
      
    
9. Show load balancing  
      
    

---

# 🎯 Strong Closing Line For Demo

“Kubernetes is not just running containers.  
It is abstracting networking, scaling, and failure handling into declarative APIs.”

  
  
  
  
  
  

FINAL DOCS  
  

  

This is good — now you're thinking like someone writing a reproducible public demo 👏

We’ll structure this cleanly so anyone can:

- Not clone anything  
      
    
- Not create files locally  
      
    
- Just run kubectl apply -f <your-github-raw-url>  
      
    

I’ll give you:

1. Multi-node cluster setup using your GitHub YAML  
      
    
2. WordPress demo deployment  
      
    
3. Your k8site deployment  
      
    
4. Your ingress deployment  
      
    

And I’ll format it exactly how it should appear in your README.

---

# 🚀 Project: k8s-GettingStarted-kindly

Repository:  
hardik-xi11  
Repo:  
k8s-GettingStarted-kindly

---

# 🔹 1️⃣ Create Multi-Node Kind Cluster (From GitHub)

First delete any existing cluster:

kind delete cluster --name k8s-demo-cluster

Now create cluster using your GitHub manifest:

kind create cluster --config https://raw.githubusercontent.com/hardik-xi11/k8s-GettingStarted-kindly/main/kind-multi-node.yaml

---

## 🎤 What You Say During Demo

“I’m creating a 4-node Kubernetes cluster locally using kind.  
The config file lives in my GitHub repo, so anyone can reproduce this setup.”

Verify:

kubectl get nodes

You should see:

- 1 control-plane  
      
    
- 3 workers  
      
    

---

# 🔹 2️⃣ Install NGINX Ingress Controller

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

Wait:

kubectl wait --namespace ingress-nginx \

 --for=condition=ready pod \

 --selector=app.kubernetes.io/component=controller \

 --timeout=120s

Verify:

kubectl get pods -n ingress-nginx

---

# 🔹 3️⃣ Deploy WordPress Demo (From Your GitHub)

Deploy directly:

kubectl apply -f https://raw.githubusercontent.com/hardik-xi11/k8s-GettingStarted-kindly/main/wordpress-demo.yaml

Verify:

kubectl get pods

kubectl get svc

---

## 🎤 What You Say

“This WordPress deployment demonstrates stateful application deployment inside Kubernetes.  
The YAML defines a Deployment, Service, and storage configuration.”

If it exposes via NodePort, access:

http://localhost:<nodeport>

---

# 🔹 4️⃣ Deploy Your k8site Application (From GitHub)

## Deployment + Service

kubectl apply -f https://raw.githubusercontent.com/hardik-xi11/k8s-GettingStarted-kindly/main/k8site.yaml

Verify:

kubectl get pods -o wide

kubectl get svc

---

## 🎤 What This Does

- Deployment creates 3 replicas  
      
    
- Service provides internal load balancing  
      
    
- Pods run your Docker image hardik0005/k8site:v3  
      
    

Explain:

“The Service abstracts pod IPs and ensures stable networking.”

---

# 🔹 5️⃣ Deploy Ingress (From GitHub)

kubectl apply -f https://raw.githubusercontent.com/hardik-xi11/k8s-GettingStarted-kindly/main/k8site-ingress.yaml

Verify:

kubectl get ingress

---

# 🔹 6️⃣ Add Hosts Entry (Required)

Windows:

127.0.0.1 k8site.local

Then access:

http://k8site.local:8080

(Remember: your kind cluster maps hostPort 8080 → container 80.)

---

# 🧠 What Each Layer Is Doing (Demo Explanation)

## Cluster

“Provides distributed orchestration across multiple nodes.”

## Deployment

“Ensures desired replica count and self-healing.”

## Service (ClusterIP)

“Provides stable internal load balancing.”

## Ingress Resource

“Defines HTTP routing rules.”

## Ingress Controller

“Implements reverse proxy logic.”

---

# 🔥 Final Demo Flow (Clean Script)

kind create cluster --config <github-url>

kubectl apply -f ingress-controller

kubectl apply -f wordpress-demo.yaml

kubectl apply -f k8site.yaml

kubectl apply -f k8site-ingress.yaml

Open:

http://k8site.local:8080

---

# 🏆 This Makes Your Repo Professional

Because now:

- Anyone can reproduce in 5 commands  
      
    
- No manual file creation  
      
    
- No local YAML editing  
      
    
- Fully GitHub-driven workflow  
      
    

That’s how production demo repos should look.