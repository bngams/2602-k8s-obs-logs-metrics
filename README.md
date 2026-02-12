# Requirements

Local kubernetes cluster + Helm


# Create namespace

kubectl create namespace obs


# Deploy a Loki monolithic multi tenant stack

Source helm chart:
https://artifacthub.io/packages/helm/grafana/loki?modal=install

Source fichier config:
https://grafana.com/docs/loki/latest/setup/install/helm/install-monolithic/#single-replica-or-multiple-replicas

Commands:

helm repo add grafana https://grafana.github.io/helm-charts

helm install my-loki grafana/loki -n obs -f my-loki-values.yaml --version 6.53.0


Note the main information after deploy (loki url....)
http://my-loki-gateway.obs.svc.cluster.local/


# Deploy grafana in the same namespace

Source helm chart:
https://artifacthub.io/packages/helm/grafana-community/grafana


Commands:

helm repo add grafana-community https://grafana-community.github.io/helm-charts/

helm install my-grafana grafana-community/grafana  -n obs 


Note the main information after deploy (grafana command to get default password....)

Note: Our current loki stack is waiting an OrgId for auth/communication so we need to add and http Header like this for datasource config in grafana: "X-Scope-OrgID"


# Deploy a promtail daemonset to send logs to promtail


Source grafana promtail doc: 
https://grafana.com/docs/loki/latest/send-data/promtail/installation/#install-as-kubernetes-daemonset-recommended

Be careful to replace values correctly to communicate with our loki
```
clients:
  - url: http://my-loki-gateway.observability.svc.cluster.local/loki/api/v1/push
    tenant_id: "1"
```

(the local config file promtail.yml in this repo should be ok with our local loki config we used before my-loki-values.yaml)


Start promtail:
kubectl apply -f promtail.yaml


