# Step by step guide to take back up of etcd and restore it 
main command for backup: ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>
main command for restore: ETCDCTL_API=3 etcdctl --data-dir <data-dir-location> snapshot restore snapshot.db

Steps :
1. We need to get 5 things to back up ETCD 
- Endpoint
- CA Certificate
- Server certificate 
- Key
- Backup location
For the first 4 we will describe ETCD pod / API-Server and get the value
For Backup location we need to refer to the question or select any path