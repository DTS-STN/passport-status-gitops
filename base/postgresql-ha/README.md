# Helm chart for PostgreSQL HA

## Downloading

To download newer versions:

``` sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo bitnami/postgresql-ha --versions | head -10
helm fetch bitnami/postgresql-ha --version {chart-version}
```

## Installation

To install: (be sure to update kubeconfig file)

``` sh
ENVIRONMENT=test

# During initial creation, passwords may be omitted to allow for auto-random password creation (10 length).
# Alternatively, you can pre-set your own passwords.
# If upgrading, fetch existing password (see bellow)

helm --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace passport-status \
    upgrade --install \
    --set persistence.size=64Gi \
    --set pgpool.adminPassword=$ADMIN_PASSWORD \
    --set pgpool.numInitChildren=64 \
    --set postgresql.username=passport-status \
    --set postgresql.database=passport-status \
    --set postgresql.password=$POSTGRESQL_PASSWORD \
    --set postgresql.repmgrPassword=$REPMGR_PASSWORD \
    $ENVIRONMENT ./postgresql-ha-10.0.1

```

To get existing passwords: (be sure to update kubeconfig file and secret names)

``` sh
ADMIN_PASSWORD=$(kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace passport-status get secret staging-postgresql-ha-pgpool -o jsonpath="{.data.admin-password}" | base64 -d)
POSTGRESQL_PASSWORD=$(kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace passport-status get secret staging-postgresql-ha-postgresql -o jsonpath="{.data.postgresql-password}" | base64 -d)
REPMGR_PASSWORD=$(kubectl --kubeconfig ~/.kube/dts-dev-rhp-akscluster.yaml --namespace passport-status get secret staging-postgresql-ha-postgresql -o jsonpath="{.data.repmgr-password}" | base64 -d)
```
