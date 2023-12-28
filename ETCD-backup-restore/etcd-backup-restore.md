## Backup and Restore etcd
How to Backup and Restore etcd Clusteretcd is a distributed key-value store that is used by Kubernetes to store and synchronize the state of the cluster. It is essential to backup etcd regularly and be able to restore it in case of a disaster. This guide will show you how to backup and restore an etcd cluster using the etcdctl command-line tool.
**Prerequisites**
- You have access to a Kubernetes cluster with etcd running as a static pod on the master node.
- You have the kubectl and etcdctl tools installed on your local machine.
- You have the following files on your local machine:
    - The CA certificate (ca.crt) used to verify the identity of the etcd server. You can find it at `/etc/kubernetes/pki/etcd/ca.crt` on the master node.
    - The server certificate (server.crt) used by the etcd server. You can find it at `/etc/kubernetes/pki/etcd/server.crt` on the master node.
    - The server key (server.key) used by the etcd server. You can find it at `/etc/kubernetes/pki/etcd/server.key` on the master node.
Taking a BackupTo take a backup of the etcd cluster, you need to perform the following steps:
- Gather the necessary information:
    - Endpoint: This is the address of the etcd cluster. You can find it by running the following command:

      `kubectl get endpoints -n kube-system etcd`

    - Backup location: This is the location where you want to store the backup file. For example, `/home/user/etcd-backup.db`.
- Take the snapshot:
    - Run the following command to take a snapshot of the etcd cluster and save it to the backup location:

      `ETCDCTL_API=3 etcdctl snapshot save <backup-file-location> --cacert=<ca-cert-file> --cert=<cert-file> --key=<key-file>`

    - For example:

    `ETCDCTL_API=3 etcdctl snapshot save /home/user/etcd-backup.db --cacert=/home/user/ca.crt --cert=/home/user/server.crt --key=/home/user/server.key`  

    - You should see a message like this:

      `Snapshot saved at /home/user/etcd-backup.db`

    - You can verify the integrity of the snapshot by running the following command:

      `ETCDCTL_API=3 etcdctl snapshot status <backup-file-location>`

    - For example:

      `ETCDCTL_API=3 etcdctl snapshot status /home/user/etcd-backup.db`

    - You should see a message like this:

      `3c0f8f32, start revision: 1, end revision: 1000, total keys: 1000`

Restoring the BackupTo restore the backup of the etcd cluster, you need to perform the following steps:
- Stop the etcd pods:
    - Run the following command to stop the etcd pods on the master node:

      `kubectl delete pods -n kube-system etcd`

    - You should see a message like this:

      `pod "etcd" deleted`

- Create a new data directory:
    - Create a new directory to store the restored data. For example:

      `mkdir /data/etcd-restored`

- Restore the snapshot:
    - Run the following command to restore the snapshot to the new data directory:

      `ETCDCTL_API=3 etcdctl snapshot restore <backup-file-location> --data-dir=<new-data-dir>`

    - For example:

      `ETCDCTL_API=3 etcdctl snapshot restore /home/user/etcd-backup.db --data-dir=/data/etcd-restored`

    - You should see a message like this:

    `Starting restore snapshot to initial cluster`
    `Restoring snapshot to /data/etcd-restored/member/snap/db`
    `Restored snapshot to /data/etcd-restored/member/snap/db`

- Start the etcd pods:
    - Run the following command to start the etcd pods on the master node:

    `kubectl apply -f /etc/kubernetes/manifests/etcd.yaml`

    - You should see a message like this:

      `pod/etcd created`

- Verify the restoration:
    - Once the etcd pods are up and running, you can verify the restoration by running the following command:

    `ETCDCTL_API=3 etcdctl get /`

    - This should return the contents of the etcd cluster.

