<details><summary><b>Take ETCD Snapshot and save it into `/tmp/snapshot.db`</b></summary><p>

```
kubectl get pod -n kube-system etcd-master -o json | jq '.spec.containers[0].command'

ETCDCTL_API=3 etcdctl \
  --cacert /etc/kubernetes/pki/etcd/ca.crt \
  --cert /etc/kubernetes/pki/etcd/server.crt \
  --key /etc/kubernetes/pki/etcd/server.key \
  snapshot save /tmp/snapshot.db
```
</p></details>

<details><summary><b>How Many Kyes in Snapshot?</b></summary><p>

```
ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/snapshot.db 
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| ba13d8bd |    11634 |        908 |     1.6 MB |
+----------+----------+------------+------------+
```
</p></details>
