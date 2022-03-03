# Solution

The `run` command is deprecated but it provides a good shortcut for creating a CronJob with a single command.

```shell
$ kubectl create cronjob current-date --schedule="* * * * *" --image=nginx -- /bin/sh -c 'echo "Current date: $(date)"'
cronjob.batch/current-date created
```

Watch the Jobs as they are executed.

```shell
$ kubectl get cronjobs --watch
NAME                      COMPLETIONS   DURATION   AGE
current-date-1557522540   1/1           3s         103s
current-date-1557522600   1/1           4s         43s
```

Identify one of the Pods (the label indicates the Job name) and render its logs.

```shell
$ kubectl get pods --show-labels
NAME                            READY   STATUS      RESTARTS   AGE   LABELS
current-date-1557522540-dp8l9   0/1     Completed   0          1m    controller-uid=3aaabf96-7369-11e9-96c6-025000000001,job-name=current-date-1557523140,run=current-date

$ kubectl logs current-date-1557522540-dp8l9
Current date: Sun Feb 14 12:40:39 UTC 2021
```

The value of the attribute `successfulJobsHistoryLimit` defines how many executions are kept in the history.

```shell
$ kubectl get cronjobs current-date -o yaml | grep successfulJobsHistoryLimit:
  successfulJobsHistoryLimit: 3
```

Finally, delete the CronJob.

```shell
$ kubectl delete cronjob current-date
```