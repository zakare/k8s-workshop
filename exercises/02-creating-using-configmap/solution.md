# Solution

Create the environment variables in the text file.

```shell
$ echo -e "DB_URL=localhost:3306\nDB_USERNAME=postgres" > config.txt
```

Create the ConfigMap and point to the text file upon creation.

```shell
$ kubectl create configmap db-config --from-env-file=config.txt
configmap/db-config created
Or
$ kubectl create configmap db-config --from-literal DB_URL=localhost:3306 --from-literal DB_USERNAME=postgres

$ kubectl run backend --image=nginx -o yaml --dry-run=client --restart=Never > pod.yaml
```

The final YAML file should look similar to the following code snippet.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: backend
  name: backend
spec:
  containers:
  - image: nginx
    name: backend
    envFrom:
      - configMapRef:
          name: db-config
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Create the Pod by pointing the `create` command to the YAML file.

```shell
$ kubectl create -f pod.yaml
```

Log into the Pod and run the `env` command.

```shell
$ kubectl exec backend -it -- /bin/sh
# env
DB_URL=localhost:3306
DB_USERNAME=postgres
...
# exit
```

## Tip

> How would you approach hot reloading of values defined by a ConfigMap consumed by an application running in Pod?

Changes to environment variables are only reflected if the Pod is restarted. 