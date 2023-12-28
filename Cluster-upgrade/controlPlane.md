
# How to upgrade your Kubernetes cluster from 1.27 to 1.28 using kubeadm

This guide explains how to upgrade a Kubernetes cluster created with kubeadm from version 1.27.x to version 1.28.x, and from version 1.28.x to 1.28.y (where y > x). Skipping MINOR versions when upgrading is unsupported. 

## Prerequisites

- The cluster should use a static control plane and etcd pods or external etcd.
- Make sure to back up any important components, such as app-level state stored in a database. kubeadm upgrade does not touch your workloads, only components internal to Kubernetes, but backups are always a best practice.
- Swap must be disabled.
- Additional information
  - The instructions below outline when to drain each node during the upgrade process. If you are performing a minor version upgrade for any kubelet, you must first drain the node (or nodes) that you are upgrading. In the case of control plane nodes, they could be running CoreDNS Pods or other critical workloads. 
  - All containers are restarted after upgrade, because the container spec hash value is changed. To verify that the kubelet service has successfully restarted after the kubelet has been upgraded, you can execute `systemctl status kubelet` or view the service logs with `journalctl -xeu kubelet`.
  - Usage of the --config flag of kubeadm upgrade with kubeadm configuration API types with the purpose of reconfiguring the cluster is not recommended and can have unexpected results. 

## Upgrade the control plane nodes

1. On your first control plane node, upgrade kubeadm:

   ```bash
   apt-mark unhold kubeadm && \
   apt-get update && apt-get install -y kubeadm=1.28.0-00 && \
   apt-mark hold kubeadm
   ```

2. Verify that the download works and has the expected version:

   ```bash
   kubeadm version
   ```

3. On the control plane node that you are upgrading, run:

   ```bash
   sudo kubeadm upgrade plan
   ```

   You should see output similar to this:

   ```bash
   [upgrade/config] Making sure the configuration is correct:
   [upgrade/config] Reading configuration from the cluster...
   [upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
   [preflight] Running pre-flight checks.
   [upgrade] Making sure the cluster is healthy:
   [upgrade] Fetching available versions to upgrade to
   [upgrade/versions] Cluster version: v1.27.2
   [upgrade/versions] kubeadm version: v1.28.0
   [upgrade/versions] Latest stable version: v1.28.0
   [upgrade/versions] Latest version in the v1.27 series: v1.27.2

   Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
   COMPONENT   CURRENT       AVAILABLE
   Kubelet     3 x v1.27.2   v1.28.0

   Upgrade to the latest version in the v1.27 series:

   COMPONENT            CURRENT   AVAILABLE
   API Server           v1.27.2   v1.28.0
   Controller Manager   v1.27.2   v1.28.0
   Scheduler            v1.27.2   v1.28.0
   Kube Proxy           v1.27.2   v1.28.0
   CoreDNS              1.8.0     1.8.0
   Etcd                 3.4.13-0  3.4.13-0

   You can now apply the upgrade by executing the following command:

       kubeadm upgrade apply v1.28.0

   Note: Before you can perform this upgrade, you have to update kubeadm to v1.28.0.

   _____________________________________________________________________
   ```

4. Choose a version to upgrade to and run the appropriate command. For example:

   ```bash
   sudo kubeadm upgrade apply v1.28.0
   ```

   You should see output similar to this:

   ```bash
   [preflight] Running pre-flight checks.
   [upgrade] Making sure the cluster is healthy:
   [upgrade/version] You have chosen to change the cluster version to "v1.28.0"
   [upgrade/versions] Cluster version: v1.27.2
   [upgrade/versions] kubeadm version: v1.28.0
   [upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
   [upgrade/prepull] Will prepull images for components [kube-apiserver kube-controller-manager kube-scheduler etcd]
   [upgrade/prepull] Prepulling image for component etcd.
   [upgrade/prepull] Prepulling image for component kube-apiserver.
   [upgrade/prepull] Prepulling image for component kube-controller-manager.
   [upgrade/prepull] Prepulling image for component kube-scheduler.
   [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
   [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-etcd
   [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-apiserver
   [apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
   [upgrade/prepull] Prepulled image for component kube-apiserver.
   [upgrade/prepull] Prepulled image for component kube-controller-manager.
   [upgrade/prepull] Prepulled image for component kube-scheduler.
   [upgrade/prepull] Prepulled image for component etcd.
   [upgrade/prepull] Successfully prepulled the images for all the control plane components
   [upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.28.0"...
   Static pod: kube-apiserver-ip-172-31-85-18 hash: d9b7af93990d702b3ee9a2beca93384b
   Static pod: kube-controller-manager-ip-172-31-85-18 hash: a640b0098f5bddc701786e007c96e220
   Static pod: kube-scheduler-ip-172-31-85-18 hash: ee7b1077c61516320f4273309e9b4690
   [upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests043818770"
   [controlplane] Adding extra host path mount "usr-share-ca-certificates" to "kube-apiserver"
   [controlplane] Adding extra host path mount "etc-ca-certificates" to "kube-apiserver"
   [controlplane] Adding extra host path mount "etc-pki" to "kube-apiserver"
   [controlplane] Adding extra host path mount "usr-share-ca-certificates" to "kube-controller-manager"
   [controlplane] Adding extra host path mount "etc-ca-certificates" to "kube-controller-manager"
   [controlplane] Adding extra host path mount "etc-pki" to "kube-controller-manager"
   [controlplane] Adding extra host path mount "usr-share-ca-certificates" to "kube-scheduler"
   [controlplane] Adding extra host path mount "etc-ca-certificates" to "kube-scheduler"
   [controlplane] Adding extra host path mount "etc-pki" to "kube-scheduler"
   [upgrade/staticpods] Preparing for "kube-apiserver" upgrade
   [upgrade/staticpods] Renewing apiserver certificate
   [upgrade/staticpods] Renewing apiserver-kubelet-client certificate
   [upgrade/staticpods] Renewing front-proxy-client certificate
   [upgrade/staticpods] Renewing apiserver-etcd-client certificate
   [upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-04-06-07-31-27/kube-apiserver.yaml"
   [upgrade/staticpods] Waiting for the kubelet to restart the component
   [upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
   Static pod: kube-apiserver-ip-172-31-85-18 hash: d9b7af93990d702b3ee9a2beca93384b
   Static pod: kube-apiserver-ip-172-31-85-18 hash: 4a4e0ee6b1cbb3aa05ecbcb4c.

