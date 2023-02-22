# Deploying environment

## Shared Resources (One per namespace e.g. nonprod, preprod, prod)

### Artemis

[ActiveMQ Artemis](./artemis/README.md)

## Deploying environment resources

### PostgreSQL

Currently each environment deploys a new PGSQL server.

[Helm chart for PostgreSQL HA](./base/postgresql-ha/README.md)

For reference, manually add passwords created during the setup to HashiCorp Vault

- PassportStatusAPI/env/{env}
  - Pgpool Admin Password
  - PostgreSQL Password
  - Repmgr Password

### Environment resources

Create ".env.api.secret" in your environment folder with secrets. See .env.api.secret.sample.

``` sh
APPLICATION_GCNOTIFY_ENGLISH_API_KEY=YOUR_KEY_HERE
APPLICATION_GCNOTIFY_FRENCH_API_KEY=YOUR_KEY_HERE
MANAGEMENT_METRICS_EXPORT_DYNATRACE_API_TOKEN=YOUR_KEY_HERE
```

Run Kustomize package

``` sh
kubectl --kubeconfig ~/.kube/dts-dev-sced-rhp-spoke-aks.yaml --namespace $NAMESPACE \
  apply \
  --kustomize ./environments/$ENVIRONMENT \
  --dry-run=server

```

## Applying Basic Auth to NGINX Ingress

[Basic Authentication - NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/examples/auth/basic/)

First thing to do is to generate user and password for the basic authentication, we will use the htpasswd.

``` sh
htpasswd -nb 'foo' 'bar'
```

Create the secret

``` sh
NAMESPACE=passport-status-preprod
ENVIRONMENT=staging
PASSWD=$(htpasswd -nb 'foo' 'bar')

# Secret with auth value
kubectl --kubeconfig ~/.kube/dts-dev-sced-rhp-spoke-aks.yaml --namespace $NAMESPACE \
  create secret generic passport-status-basic-auth-$ENVIRONMENT \
  --from-literal=auth=$PASSWD \

# Add labels to secret
kubectl --kubeconfig ~/.kube/dts-dev-sced-rhp-spoke-aks.yaml --namespace $NAMESPACE \
  label secrets passport-status-basic-auth-$ENVIRONMENT \
  app.kubernetes.io/instance=$ENVIRONMENT \
  app.kubernetes.io/part-of=$NAMESPACE

```

Apply the proper annotations to the ingress

``` yaml

# type of authentication
nginx.ingress.kubernetes.io/auth-type: basic
# name of the secret that contains the user/password definitions
nginx.ingress.kubernetes.io/auth-secret: passport-status-basic-auth-staging
# message to display with an appropriate context why the authentication is required
nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required'

```
