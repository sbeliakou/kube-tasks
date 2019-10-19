<details><summary><b>Take ETCD Snapshot and save it into `/tmp/snapshot.db`</b></summary><p>

Here's just an example of commands:

```
kubectl get pod -n kube-system etcd-master -o json | jq '.spec.containers[0].command'

ETCDCTL_API=3 etcdctl \
  --cacert /etc/kubernetes/pki/etcd/ca.crt \
  --cert /etc/kubernetes/pki/etcd/server.crt \
  --key /etc/kubernetes/pki/etcd/server.key \
  snapshot save /tmp/snapshot.db
```

<form method="POST" action="/" style="text-align: center;">
  <button type="submit" class="btn btn-primary">Check it!</button>  
</form>
</p></details>

<details><summary><b>How Many Keys in this Snapshot?</b></summary><p>

```
ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/snapshot.db 
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| ba13d8bd |    11634 |        908 |     1.6 MB |
+----------+----------+------------+------------+
```
</p></details>

<details><summary><b>Create a DaemonSet which is supposed to run on ALL nodes (masters+workers)</b></summary><p>

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: sleep-daemon
  name: sleep-daemon
spec:
  selector:
    matchLabels:
      app: sleep-daemon
  template:
    metadata:
      labels:
        app: sleep-daemon
    spec:
      containers:
      - image: busybox
        name: busybox
        command:
        - sleep
        - "1000"
```
</p></details>

<details><summary><b>Create a DaemonSet which is saving its data into /var/ds-data foler on each host of the cluster</b></summary><p>

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: sleep-daemon
  name: sleep-daemon
spec:
  selector:
    matchLabels:
      app: sleep-daemon
  template:
    metadata:
      labels:
        app: sleep-daemon
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - image: busybox
        name: busybox
        command:
        - /bin/sh
        - -c
        - touch /var/ds-data/file && sleep 10000
        volumeMounts:
        - name: data-folder
          mountPath: /var/ds-data
      volumes:
      - name: data-folder
        hostPath:
          path: /var/ds-data
          type: DirectoryOrCreate
```
</p></details>
