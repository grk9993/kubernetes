
# How to upgrade a Kubernetes worker node from 1.27 to 1.28 using kubeadm

This guide explains how to upgrade a Kubernetes worker node created with kubeadm from version 1.27.x to version 1.28.x. Skipping MINOR versions when upgrading is unsupported. 

## Prerequisites


- The cluster should use a static control plane and etcd pods or external etcd.
- Make sure to back up any important components, such as app-level state stored in a database. kubeadm upgrade does not touch your workloads, only components internal to Kubernetes, but backups are always a best practice.
- Swap must be disabled.
- Additional information
  - The instructions below outline when to drain each node during the upgrade process. If you are performing a minor version upgrade for any kubelet, you must first drain the node (or nodes) that you are upgrading. In the case of control plane nodes, they could be running CoreDNS Pods or other critical workloads. 
  - All containers are restarted after upgrade, because the container spec hash value is changed. To verify that the kubelet service has successfully restarted after the kubelet has been upgraded, you can execute `systemctl status kubelet` or view the service logs with `journalctl -xeu kubelet`.
  - Usage of the --config flag of kubeadm upgrade with kubeadm configuration API types with the purpose of reconfiguring the cluster is not recommended and can have unexpected results. 

## Upgrade the worker node

1. On the worker node, upgrade kubeadm:

   ```bash
   apt-mark unhold kubeadm && \
   apt-get update && apt-get install -y kubeadm=1.28.0-00 && \
   apt-mark hold kubeadm
   ```

2. Verify that the download works and has the expected version:

   ```bash
   kubeadm version
   ```

3. On the worker node, run:

   ```bash
   sudo kubeadm upgrade node
   ```

   This upgrades the local kubelet configuration required for a worker node.

4. Drain the node before upgrading the kubelet:

   ```bash
   # execute this command on a control plane node
   # replace <node-to-drain> with the name of your node you are draining
   kubectl drain <node-to-drain> --ignore-daemonsets
   ```

   This prepares the node for maintenance by marking it unschedulable and evicting the workloads.

5. Upgrade the kubelet and kubectl:

   ```bash
   # replace x in 1.28.x-* with the latest patch version
   apt-mark unhold kubelet kubectl && \
   apt-get update && apt-get install -y kubelet=1.28.x-* kubectl=1.28.x-* && \
   apt-mark hold kubelet kubectl
   ```

6. Restart the kubelet:

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart kubelet
   ```

7. Uncordon the node:

   ```bash
   # execute this command on a control plane node
   # replace <node-to-drain> with the name of your node
   kubectl uncordon <node-to-drain>
   ```

   This marks the node as schedulable and allows new pods to be scheduled on it.

## Verify the status of the cluster

After the upgrade, you can verify the status of the cluster by running:

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

You should see that all nodes are in the Ready state and all pods are running. You can also check the version of the cluster components by running:

```bash
kubectl version
kubectl describe nodes | grep -i kubelet
```

You should see that the cluster version and the kubelet version are both 1.28.x.

