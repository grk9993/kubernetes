# This code sets up an alias 'k' for the 'kubectl' command.
- `alias k=kubectl`

# This code sets up autocompletion for the 'k' alias using the __start_kubectl function.
- `complete -o default -F __start_kubectl k`

# Set Current Context Namespace
- `k config set-context --current --namespace=my-ns`

# Creates a deployment with nginxdemos/hello image and 3 replicas.
- `k create deploy mydeployment --image=nginxdemos/hello --replicas=3`

# Exposes the deployment as a NodePort service on port 80.
- `k expose deploy mydeployment --type=NodePort --port=80 --target-port=80 --dry-run=client -o yaml`

# Creates a NodePort service with port mapping and node port specified.
- `k create svc nodeport mydeployment --tcp=80:80 --node-port=30009 --dry-run=client -o yaml`

# This command replaces a Kubernetes pod using the specified JSON file.
# It forces the replacement, even if the pod is not ready for termination.
- `k replace --force -f ./pod.json`

# Creates a ConfigMap with username and password literals.
- `k create cm webConfigmap --from-literal=username='myuser' --from-literal=password='pswd' -o yaml --dry-run=client`

# Creates a generic secret with username and password literals.
- `k create secret generic websecret --from-literal=username='myuser' --from-literal=password='pswd' -o yaml --dry-run=client`

# Retrieves and decodes the password from the secret.
- `k get secrets websecret -o json |jq -r .data.password |base64 --decode && echo`

# Updates the image of the deployment to nginxdemos/hello.
- `k set image deploy/mydeployment hello=nginxdemos/hello`

# Shows the rollout history of the deployment.
- `k rollout history deployment mydeployment`

# Retrieves the list of nodes with custom columns.
- `k get nodes -o custom-columns=NODE_NAME:.metadata.name,TAINTS:.spec.taints`

# Applies a taint to the node with specified key-value pair and effect.
- `k taint nodes docker-desktop my-key=my-value:NoSchedule`

# Removes the specified taint from the node.
- `k taint nodes docker-desktop my-key=my-value:NoSchedule-`

# Drains the node by evicting all pods except daemonsets.
- `k drain --ignore-daemonsets docker-desktop`

kubectl cordon my-node 
kubectl api-resources