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

A: `k get nodes -o jsonpath='{ .items[].status.nodeInfo.osImage}' >/root/osimages.txt`

Explanation:
The command uses `kubectl` to get information about all nodes. The `-o jsonpath` option is used to specify the output format and the JSON Path query to extract the required information. The JSON Path query `{ .items[].status.nodeInfo.osImage}` navigates through each node's status to the `nodeInfo` section and retrieves the `osImage` value. The `>` operator is used to redirect the output into the file `/root/osimages.txt`.

