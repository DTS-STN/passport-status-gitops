# Deploying environment

## Shared Resources (One per namespace e.g. nonprod, preprod, prod)

### Artemis

[ActiveMQ Artemis](./artemis/README.md)

## Deploying environment resources

### PostgreSQL

Currently each environment deploys a new PGSQL server.

[Helm chart for PostgreSQL HA](./base/postgresql-ha/README.md)

Add passwords created during the setup to HashiCorp Vault

- PassportStatusAPI/env/{env}
  - Pgpool Admin Password
  - PostgreSQL Password
  - Repmgr Password

### Environment resources

Manually add API Secrets before running the environment setup.

- passport-status-api-{env}
  - APPLICATION_GCNOTIFY_API_KEY
  - MANAGEMENT_METRICS_EXPORT_DYNATRACE_API_TOKEN

``` sh

NAMESPACE=passport-status
ENVIRONMENT=test
APPLICATION_GCNOTIFY_API_KEY=[GC_NOTIFY_KEY_HERE]
MANAGEMENT_METRICS_EXPORT_DYNATRACE_API_TOKEN=[DYNATRACE_TOKEN]

# Secret with two values
kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace $NAMESPACE \
  create secret generic passport-status-api-$ENVIRONMENT \
  --from-literal=APPLICATION_GCNOTIFY_API_KEY=$APPLICATION_GCNOTIFY_API_KEY \
  --from-literal=MANAGEMENT_METRICS_EXPORT_DYNATRACE_API_TOKEN=$MANAGEMENT_METRICS_EXPORT_DYNATRACE_API_TOKEN \

# Add labels to secret
kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace $NAMESPACE \
  label secrets passport-status-api-$ENVIRONMENT \
  app.kubernetes.io/instance=$ENVIRONMENT \
  app.kubernetes.io/part-of=$NAMESPACE

```

Run Kustomize package

``` sh
kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace $NAMESPACE \
  apply \
  --kustomize ./environments/$ENVIRONMENT \
  --dry-run=server

```
