# monitoring-sample

monitoring sample of following containers. 

|  containers   |        url        |
| ------------- | ----------------- |
| prometheus    | http://${IP}:9090 |
| node-exporter | http://${IP}:9100 |
| push-gateway  | http://${IP}:9091 |
| grafana       | http://${IP}:3000 |
| alertmanager  | http://${IP}:9093 |

## usage

```sh
$ docker-compose up
```

## test

POST metrics by following.

```log
$ HOST_IP=192.168.104.121
$ # POST
$ cat <<EOF | curl --data-binary @- http://${HOST_IP}:9091/metrics/job/some_job/instance/some_instance
# TYPE some_metric counter
some_metric{label="val1"} 42
# TYPE another_metric gauge
# HELP another_metric Just an example.
another_metric 2398.283
EOF
$
```

Then check metrics "some_metric" on push-gateway & prometheus UI.

POST following metric to firing alert.

```sh
$ cat <<EOF | curl --data-binary @- http://${HOST_IP}:9091/metrics/job/some_job/instance/some_instance
  # TYPE up counter
  up 0
EOF
$
```

Then check metrics "up" listed on push-gateway UI and alert status is "PENDING" on prometheus UI.

After 1m, check alert status change form "PENDING" to "FIRING" on prometheus UI and it listed on alertmanager UI.

POST following metric to stopping alert.

```sh
cat <<EOF | curl --data-binary @- http://${HOST_IP}:9091/metrics/job/some_job/instance/some_instance
  # TYPE up counter
  up 1
EOF
```

Check alert status change form "FIRING" to "0 active" on prometheus UI and unlisted from alertmanager UI.
