## WIP TICK Stack Deploy to GKE

To install the TIC+Grafana stack to a K8s cluster:

set your gcloud config to the desired project and ensure that you are hitting the appropriate cluster with kubectl. 

make sure that you have helm installed on the system that you deploy from, either your laptop for accessible clusters or the bastion host for private clusters. 

make sure that you have added the stable repo to your helm config

```helm repo add stable https://kubernetes-charts.storage.googleapis.com/```

### helm installs

to install telegraph run the following helm command:

```helm install stable/telegraf --generate-name```

output
```yaml
NAME: telegraf-1581972219
LAST DEPLOYED: Mon Feb 17 14:43:41 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
```
    - the --generate name flag will autogenerate a name for the telegraf workload. you can also declare a name in the command.

to install influxdb, run the follow helm command:

```helm install influxdb-matador stable/influxdb```

output
```yaml
NAME: influxdb-matador
LAST DEPLOYED: Mon Feb 17 14:44:53 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
```

    - instead of using the --generate-name flag, this example declares the name of the workload (you do not need to use the --name flag)

to patch influxdb to be externally accessible

```bash
kubectl patch svc "$APP_INSTANCE_NAME-influxdb-svc" \
  --namespace "$NAMESPACE" \
  --patch '{"spec": {"type": "LoadBalancer"}}'
```

to install chrongraf: 

```helm install chronograf-matador stable/chronograf```

output
```yaml
NAME: chronograf-matador
LAST DEPLOYED: Mon Feb 17 14:47:24 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
```

to patch the chronograf workload to be accessible without port-forwarding (testing purposes only at this point)
```bash
kubectl patch svc "prometheus-grafana" --namespace "prometheus" -p '{"spec": {"type": "LoadBalancer"}}'
  ```

kubectl get secret --namespace prometheus prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

http://35.193.238.66/invite/nrtWgyDrCb2GNlM1x1swex8Hb9Gl2i

kubectl exec -it $(kubectl get pods -l app=cosmo --output=json | jq -r .items[0].metadata.name) -c cosmo -- /bin/bash
