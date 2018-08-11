# This repo will help you enable a demo flow with [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) using the Service Catalog Namespaced Broker Resources and Catalog Restrictions capabilities


## Setup Service Catalog

Note: disable the health checks, they don't work well on AKS at the moment

```console
helm install svc-cat/catalog --name catalog --namespace catalog \ 
--set apiserver.healthcheck.enabled=false \
--set controllerManager.healthcheck.enabled=false 
```

## Install OSBA 

Install OSBA, don't register it. Note, we haven't pushed a new helm chart yet, so use v1.1.0 in the tag:

```console
helm install azure/open-service-broker-azure --name osba --namespace osba   --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID   --set azure.tenantId=$AZURE_TENANT_ID   --set azure.clientId=$AZURE_CLIENT_ID   --set azure.clientSecret=$AZURE_CLIENT_SECRET   --set registerBroker=false --set modules.minStability=experimental --set image.tag=v1.1.0
```

## Register Service Broker in a Namespace

### No Restrictions

```console
helm install ./broker-registrar/ --name osba-test --namespace osba-test --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local
```

### Only the service "azure-postgresql-9-6"

```console
helm install ./broker-registrar/ --name osba-test-2 --namespace osba-test-2 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.classes={"spec.externalName=azure-postgresql-9-6"}
```

### Only plans named "basic"

```console
helm install ./broker-registrar/ --name osba-test-3 --namespace osba-test-3 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.plans={"spec.externalName==basic"}
```

#Only the "basic" plan from service "azure-postgreqsl-9-6"

```console
helm install ./broker-registrar/ --name osba-test-4 --namespace osba-test-4 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.classes={"spec.externalName=azure-postgresql-9-6"} --set catalog.restrictions.plans={"spec.externalName==basic"}
```

