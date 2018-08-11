First thing: disable the health checks, they don't work well on AKS :(


helm install svc-cat/catalog --name catalog --namespace catalog \ 
--set apiserver.healthcheck.enabled=false \
--set controllerManager.healthcheck.enabled=false 


Install OSBA, don't register it. Note, we haven't pushed a new helm chart yet, so use v1.1.0 in the tag:

helm install azure/open-service-broker-azure --name osba --namespace osba   --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID   --set azure.tenantId=$AZURE_TENANT_ID   --set azure.clientId=$AZURE_CLIENT_ID   --set azure.clientSecret=$AZURE_CLIENT_SECRET   --set registerBroker=false --set modules.minStability=experimental --set image.tag=v1.1.0


# No Restrictions

helm install ./broker-registrar/ --name osba-test --namespace osba-test --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local


# Only the service "azure-postgresql-9-6"

helm install ./broker-registrar/ --name osba-test-2 --namespace osba-test-2 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.classes={"spec.externalName=azure-postgresql-9-6"}

# Only plans named "basic"
helm install ./broker-registrar/ --name osba-test-3 --namespace osba-test-3 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.plans={"spec.externalName==basic"}


#Only the "basic" plan from service "azure-postgreqsl-9-6"

helm install ./broker-registrar/ --name osba-test-4 --namespace osba-test-4 --set broker.url=https://osba-open-service-broker-azure.osba.svc.cluster.local --set catalog.restrictions.classes={"spec.externalName=azure-postgresql-9-6"} --set catalog.restrictions.plans={"spec.externalName==basic"}

