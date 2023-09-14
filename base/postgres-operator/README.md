# Crunchy Postgresql Operator

The Kustomize files found in this directory have been copied from the [Crunchy PostgreSQL Operator examples
repository](https://github.com/CrunchyData/postgres-operator-examples/) and modified to fit the needs of the Passport
Status Checker.

Specifically, names of various resources have been modified to be more consistent and readable (ie: `pgo` →
`postgres-operator`).

## Installing CRDs

This Kustomize package does not include the Crunchy PGO CRDs, since development teams have no access to install CRDs in
the DSHP Kubernetes clusters.

If installing this package to the DSHP clusters, we must ask the SRE team to install the CRDs first. If installing this
package to another cluster, ensure that you first install the CRDs yourself.

For reference, here are the commands needed to install these CRDs:

``` sh
curl https://raw.githubusercontent.com/CrunchyData/postgres-operator/master/config/crd/bases/postgres-operator.crunchydata.com_pgupgrades.yaml | kubectl create --filename -
curl https://raw.githubusercontent.com/CrunchyData/postgres-operator/master/config/crd/bases/postgres-operator.crunchydata.com_postgresclusters.yaml | kubectl create --filename -
```

## References

- <https://access.crunchydata.com/documentation/postgres-operator/v5/quickstart/>
