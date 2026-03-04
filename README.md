# Newbie Guide to Kubernetes with Kind


  

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
  
  
  ###  To Delete your cluster use:
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

### Use the following Wordpress Manifest to deploy a Wordpress and a MySQL containers

Wordpress manifest
```

	kubectl apply -f https://github.com/hardik-xi11/k8s-GettingStarted-kindly/raw/refs/heads/master/wordpress-demo.yaml	
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


### apply the nginx ingress controller manifest to create an ingress controller

```bash

	kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

```
---

Check the status of the ingress deployment!
```bash

	kubectl get pods -n ingress-nginx -w

```

### Create an Ingress Service for the web app!

Ingress manifest for site
```

	kubectl apply -f https://raw.githubusercontent.com/hardik-xi11/k8s-GettingStarted-kindly/refs/heads/master/k8site-ingress.yaml
	
```
### Now deploy the manifes for the web app which will also pull the docker image of the web app
Site manifest

```

	kubectl apply -f https://github.com/hardik-xi11/k8s-GettingStarted-kindly/raw/refs/heads/master/k8site.yaml
	
```







































































### Uninstall and wrapping up!

```powershell

	winget uninstall --id Kubernetes.kubectl

```

```powershell

	winget uninstall --id Kubernetes.kind

```