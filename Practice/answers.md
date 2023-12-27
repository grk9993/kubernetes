## Q: List all pods by age in ascending order:
A: `k get po -A --sort-by=.metadata.creationTimestamp | tac`

Explanation: This command lists all pods in ascending order based on their creation timestamp. The `k get po -A` command retrieves all pods from all namespaces, and the `--sort-by=.metadata.creationTimestamp` flag sorts the pods by their creation timestamp. The `tac` command is used to reverse the order of the output, resulting in ascending order.

---

## Q: Create a static pod on node01
A: 
1. Go to node01 and run `ps -aux | grep kubelet` to get the `config.yaml` file.
2. Use `cat` to view the file and search for `staticPod`.
3. The file is usually located in `/etc/kubernetes/manifests`.
4. Create a `pod.yaml` file there.

Explanation: To create a static pod on node01, you need to locate the `config.yaml` file of the kubelet process running on that node. Running `ps -aux | grep kubelet` will help you find the process and its associated configuration file. Once you have the `config.yaml` file, use `cat` to view its contents and search for the `staticPod` section. Typically, the `config.yaml` file is located in the `/etc/kubernetes/manifests` directory. Create a `pod.yaml` file in that directory to define your static pod.

---

## Q: Create a multicontainer pod with nginx and busybox
A: See `multipod.yaml` for an example.

Explanation: To create a multicontainer pod with nginx and busybox, you can refer to the `multipod.yaml` file for an example configuration. The `multipod.yaml` file will contain the necessary specifications to define a pod with multiple containers.

---

## Q: Create a pod in defense namespace with image nginx:1.17, with labels env=dev and tier=front
A: `k run delta-pod -n defense --image=nginx:1.17 --labels="env=dev,tier=front" --dry-run=client -o yaml > delta-pod.yaml`

Explanation: This command creates a pod named `delta-pod` in the `defense` namespace with the image `nginx:1.17`. The pod is also assigned labels `env=dev` and `tier=front`. The `--dry-run=client -o yaml` flags ensure that the command only generates the YAML configuration for the pod without actually creating it. The generated YAML is then redirected to a file named `delta-pod.yaml`.

---

## Q: Create a pod named admin-pod with image busybox, which should sleep for 3200. Also, give permission for setting system time.
A: See `admin-pod.yaml` for an example.

Explanation: To create a pod named `admin-pod` with the image `busybox` and a sleep duration of 3200 seconds, you can refer to the `admin-pod.yaml` file for an example configuration. The `admin-pod.yaml` file will contain the necessary specifications to define the pod, including the sleep duration. Giving permission for setting the system time is done by adding the `SYS_TIME` capability.

---

## Q: Create a deployment web-proj-268 with image nginx:1.16 and 1 replica. Upgrade the deployment to nginx:1.17 using rolling update. The version upgrade should be recorded in resource annotations.
A: 
1. `k create deploy web-proj-268 --image=nginx:1.16 --replicas=1`
2. `k set image deploy/web-proj-268 nginx=nginx:1.17 --record` (Note: `--record` is deprecated)
3. `kubectl annotate deployment/web-proj-268 kubernetes.io/change-cause="image updated to nginx:1.17"` (New way)

Explanation: This set of commands creates a deployment named `web-proj-268` with the image `nginx:1.16` and 1 replica. The deployment is then upgraded to use the image `nginx:1.17` using a rolling update. The `--record` flag is used to record the version upgrade in the deployment's resource annotations. However, note that the `--record` flag is deprecated, and the recommended way to annotate the deployment with the change cause is by using the `kubectl annotate` command.

---

## Q: Create a pod called web-pod using nginx. Expose it internally with a service called web-pod-svc. Use the image busybox:1.28 for DNS lookup. Record the result in /root/web-svc.svc and /root/web-pod.pod.
A: 
1. `k run web-pod --image=nginx`
2. `k expose pod web-pod --name=web-pod-svc --port 80`
3. `k run dnslookup --image=busybox:1.28 --command sleep 4800`
4. `k exec dnslookup -- nslookup web-pod-svc > /root/web-svc.svc`
5. `k exec dnslookup -- nslookup 10-1-0-67.default.pod.cluster.local > /root/web-pod.pod`

Explanation: This set of commands creates a pod named `web-pod` using the `nginx` image. It then exposes the pod internally by creating a service named `web-pod-svc` with port 80. Additionally, a separate pod named `dnslookup` is created using the `busybox:1.28` image to perform DNS lookups. The results of the DNS lookups for `web-pod-svc` and `10-1-0-67.default.pod.cluster.local` are recorded in the files `/root/web-svc.svc` and `/root/web-pod.pod`, respectively.

## Q: Use JSON Path query to get OSImages of all nodes and store it in file /root/osimages.txt
Note: The OSImages are under nodeInfo section under status of each node

A: `k get nodes -o jsonpath='{ .items[*].status.nodeInfo.osImage}' >/root/osimages.txt`

Explanation:
The command uses `kubectl` to get information about all nodes. The `-o jsonpath` option is used to specify the output format and the JSON Path query to extract the required information. The JSON Path query `{ .items[].status.nodeInfo.osImage}` navigates through each node's status to the `nodeInfo` section and retrieves the `osImage` value. The `>` operator is used to redirect the output into the file `/root/osimages.txt`.

## Q: Create Persistant Volume with given specification
volume name: pv-rnd
storage: 100Mi
Access mode: ReadWriteMany
host-path: /pv/host_data-rnd

A: See `pv-rnd.yaml` for an example.

Explanation: To create a Persistent Volume (PV) with the given specifications, you can refer to the `pv-rnd.yaml` file for an example configuration. The PV is named `pv-rnd` and has a storage capacity of 100Mi. It has an access mode of ReadWriteMany, allowing multiple pods to read and write to it simultaneously. The PV is backed by a host path located at `/pv/host_data-rnd`.

## Q: List all InternalIP of all the nodes and store the output in internal_IP.txt file
Note: All IPs should be in a single line separated by space

A: `k get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}' > internal_IP.txt`

Explanation: 
This command retrieves the InternalIP addresses of all nodes in the cluster using the `kubectl get nodes` command. The `-o jsonpath` option is used to specify the output format and the JSON Path query `{.items[*].status.addresses[?(@.type=="InternalIP")].address}` is used to extract the InternalIP addresses. The `>` operator is used to redirect the output into the `internal_IP.txt` file. The InternalIP addresses are stored in a single line, separated by spaces, as specified in the note.


## Q: Create a deployment web-003 scale it to 3 make sure that desired number of pods are always running
A:  k create deploy web-003 --image=nginx --replicas=3 -o yaml>web-003.yaml
See the details in `web-003.yaml`.
Than edit `web-003.yaml` to make `maxUnavailable: 0%`

## Q: Create a pod front-end-helper that writes "Binaries downloaded successfully" in frontend-helper-log.txt and exit. Pod front-end-helper should automatically get deleted after completed
A:  `k run front-end-helper --image=busybox -it --rm --restart=Never  -- /bin/sh -c 'echo Binary downloaded successfully' > frontend-helper-log.txt`

Explanation: This command creates a pod named `front-end-helper` using the `busybox` image. The pod runs an interactive shell (`/bin/sh`) command to echo the message "Binaries downloaded successfully" and redirects the output to a file named `frontend-helper-log.txt`. The `--rm` flag ensures that the pod is automatically deleted after it completes, and the `--restart=Never` flag prevents the pod from being restarted if it fails or is terminated.

## Q: List all the nodes with taints whose effect is NoSchedule in format Name, Taint value in file custom-columns-cka.log
A: `k get nodes -o custom-columns="name:{.metadata.name},taints:.spec.taints[?(@.effect=='NoSchedule')].value" >custom-columns-cka.log`

Explanation: This command lists all the nodes with taints whose effect is NoSchedule. The `-o custom-columns` flag is used to specify the output format. The custom column format is defined as `name:{.metadata.name},taints:.spec.taints[?(@.effect=='NoSchedule')].value`, which displays the node name and the value of the taints with the effect NoSchedule. The output is redirected to the file `custom-columns-cka.log`.

## Q: Create a namespace called airfusion,create networkpolicy that allows pods in ns airfusion to receive traffic on port 80 from other pods in same ns but no communication is allowed from outside ns
A: `k create ns airfusion`
See `networkPolicy.yaml` for an example.
Explanation: To create a namespace called `airfusion` and a network policy that allows pods within the `airfusion` namespace to receive traffic on port 80 from other pods within the same namespace, but restricts communication from outside the namespace, you can use the following commands:

1. `k create ns airfusion`: This command creates the `airfusion` namespace.
2. See `networkPolicy.yaml` for an example: This refers to a YAML file named `networkPolicy.yaml` that contains the configuration for the network policy. The file should define the necessary rules to allow traffic on port 80 within the `airfusion` namespace while blocking communication from outside the namespace.

## Q: Expose a web pod audit-web-app. Name of service should be audit-web-app-service. service should be available on port 30002 on all nodes of cluster
A:  `k expose pod audit-web-app --type=NodePort --port=80 --target-port=80 -o yaml --dry-run=client > exposePod.yaml`
After generating file edit it and add nodePort: 30002
`k apply -f exposePod.yaml`

Explanation: 
To expose the web pod named `audit-web-app` and create a service named `audit-web-app-service` that is available on port 30002 on all nodes of the cluster, you can use the following steps:

1. Run the command `k expose pod audit-web-app --type=NodePort --port=80 --target-port=80 -o yaml --dry-run=client > exposePod.yaml` to generate a YAML file named `exposePod.yaml` that contains the configuration for the service.
2. Open the `exposePod.yaml` file and add `nodePort: 30002` under the `spec` section to specify the desired node port.
3. Apply the modified configuration by running the command `k apply -f exposePod.yaml`.

This will create the service `audit-web-app-service` and expose it on port 30002 on all nodes of the cluster.

## Q: create a pod named prod-pod-nginx with toleration for taint env=prod:NoSchedule
A: See the details in `tolarations.yaml`.

Explanation: 
To create a pod named `prod-pod-nginx` with a toleration for the taint `env=prod:NoSchedule`, you can refer to the `tolarations.yaml` file for the detailed configuration. The `tolarations.yaml` file should contain the necessary specifications to define the pod and include the toleration for the specified taint. Tolerations allow pods to be scheduled on nodes with matching taints, in this case, allowing the `prod-pod-nginx` pod to be scheduled on nodes with the `env=prod:NoSchedule` taint.

## Q: Create a pod with the following details:
- Name: redis-pod
- runAsUser: 1000
- runAsGroup: 3000
- fsGroup: 2000
- Image: redis:alpine 

A: See the details in `runasuser.yaml`.

Explanation:
To create a pod named `redis-pod` with the specified configuration, you can refer to the `runasuser.yaml` file for the detailed configuration. The `runasuser.yaml` file should contain the necessary specifications to define the pod, including the `runAsUser`, `runAsGroup`, `fsGroup`, and `image` values. These settings define the user and group IDs under which the pod's containers will run, as well as the file system group ownership and the image to use for the pod.

## Q: Create a PersistentVolume with following specification:
- Volume Name : mypvlog
- Storage: 100Mi
- Access Modes: ReadWriteMany
- Host Path : /pv/log
- Reclaim Policy : Retain

A: See the details in `persistentvolume.yaml`.

Explanation:
To create a PersistentVolume (PV) with the specified specifications, you can refer to the `persistentvolume.yaml` file for the detailed configuration. The PV is named `mypvlog` and has a storage capacity of 100Mi. It has an access mode of ReadWriteMany, allowing multiple pods to read and write to it simultaneously. The PV is backed by a host path located at `/pv/log`. The reclaim policy is set to Retain, which means that the PV will not be automatically deleted when the corresponding PersistentVolumeClaim (PVC) is deleted.

## Q: Create a PersistentVolumeClaim with following specification:
- Volume Name : pv-claim-log
- Storage Request: 50Mi
- Access Modes: ReadWriteMany

A: See the details in `persistent-volume-claim.yaml`.

Explanation:
To create a PersistentVolumeClaim (PVC) with the specified specifications, you can refer to the `persistent-volume-claim.yaml` file for the detailed configuration. The PVC is named `pv-claim-log` and requests a storage capacity of 50Mi. It has an access mode of ReadWriteMany, allowing multiple pods to read and write to it simultaneously.

## Q: Create a Pod with the following specification:
- Name: my-nginx-pod
- Image: nginx
- Volume: PersistentVolumeClaim=pv-claim-log
- Volume Mount: /log

A: See the details in `pvc-pod.yaml`.

Explanation:
To create a Pod named `my-nginx-pod` with the specified configuration, you can refer to the `pvc-pod.yaml` file for the detailed configuration. The `pvc-pod.yaml` file should contain the necessary specifications to define the Pod, including the name, image, volume, and volume mount. The Pod uses the `nginx` image and mounts the `pv-claim-log` PersistentVolumeClaim to the `/log` directory within the Pod's filesystem.


