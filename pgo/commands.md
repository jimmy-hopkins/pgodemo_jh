## Operator Deploy
cd postgres-operator-examples/kustomize
oc apply --server-side -k install/default

## Monitoring Deploy
cd postgres-okperator-examples/kustomize
kubectl apply -k monitoring -n pgmonitor

## Create Hippo Cluster
kubectl apply -k hippo -n crunchy

## Load Simulation
kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- psql -c "alter user postgres with password 'welcome1'"

kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- pgbench -i -s 20

kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- pgbench -T 600 -j 2 -c 10

export DATABASE_URL='user=postgres password=welcome1 host=hippo-openshift port=5432 database=postgres sslmode=require'
./pgSimload_mac

## HA
kubectl exec -it -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- bash

cd /pgdata
ls pg14
rm -rf pg14*
ls


