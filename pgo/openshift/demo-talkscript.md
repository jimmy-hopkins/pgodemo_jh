# Crunchy Data Postgres Operator for Kubernetes v5.1 Demo

## Setup
The following items should be setup prior to the demo:
-   Ensure the all postgres clusters have been deleted.
-   Uninstall Operator
-   Create the following namespaces
    -   crunchy
    -   pgmonitor
    -   postgres-operator
-   Deploy LoadBalancer services
    -   hippo-lb
    -   grafana-lb
-   Get IP address for LoadBalancer and update /etc/hosts on local machine
-   Open VSCode to pgodemo/openshift
-   Open 1 Terminal window
-   Open 2 & 3 Terminal window in split mode
-   Start watch command in one of the split windows and get ready to run sim load in other
-   Deploy pgMonitor

```
cd postgres-okperator-examples/kustomize
kubectl apply -k monitoring -n pgmonitor
```


## Slide 1: Welcome
Good day and thank you for this opportunity to show off the Crunchy Data Operator.  Here at Crunchy we are extremely proud of the Operator and the value add it brings to our clients.

My name is Brian Pace.  I have been running databases in large enterprises now for over 30 years.  Of those, 15 was spent as the global database technical lead at Citi.

> Question:
> Are you running Postgres in Kubernetes today?
> - If not, what is the obstacles?
> - If so, what are some of the challenges you have faced?

## Slide 2: Expert Knowledge in 2 Domains
What does it take to run Postgres in Kubernetes?

It takes **expert** knowledge in two different domains: Postgres and Kubernetes

That is a tremendous requirement.  It is hard enough hiring a resource with expert knowledge in one field, much less two fields.


## Slide 3:  Knowledge in Code
This expert knowledge requirement is the exact problem that the Operator solves.

“Knowledge in Code”

The Operator understands both domains and what is required to safely, efficiently, and effectively run Postgres in Kubernetes.

Crunchy Data has and continues to invest heavily in mastering these two domains so you can focus on your business goals (a better investment).

## Slide 4: Agenda
Over the next few minutes, I want to demonstrate this knowledge in action:
- Simplicity of the Solution
- Operations (monitoring, backups)
- High Availability


## Slide 5: Simplicity of the Solution
> **ACTION:**
> - Deploy Operator
> - Show Postgres manifest (Declarative Postgres)
> - Create first cluster

```
cd postgres-operator-examples/kustomize
oc apply --server-side -k install/default
kubectl apply -k hippo -n crunchy
```

By submitting this simple manifest that describes desired Postgres cluster, the Operator takes care of the complex work of creating the necessary Kubernetes objects and 'wiring' up the solution.

> **PAUSE FOR QUESTIONS**

That demonstrates how simply the Operator makes running Postgres in Kubernetes.

## Slide 6: Operations
Now, let’s look at some things that Operations is concerned about.  Amoung the many concerns that Operation Teams have is  **Monitoring**, **Backups**, **Patching**.

> **ACTION: Setup**
> - Start pgBench to generate load
> - Start application load sim program

```
kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- psql -c "alter user postgres with password 'welcome1'"
kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- pgbench -i -s 20
kubectl exec -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- pgbench -T 600 -j 2 -c 10

export DATABASE_URL='user=postgres password=welcome1 host=hippo-openshift port=5432 database=postgres sslmode=require'
../pgSimload_mac
```

> **ACTION: Monitoring**
> - Deploy pgMonitor
> - In the background generate load
> - Show monitoring dashboards

> **ACTION: Backups**
> - Show scheduled backup jobs

> **ACTION: Patching**
> - Performing rolling minor version upgrade
> - Highlight minimal disruption to application

```
kubectl apply -k hippo -n crunchy
```

## Slide 7: High Availability
So far we have seen how the Operator takes on the complicated behind the scenes work and makes it very easy to run postgres in kubernettes.

We also just reviewed a few of the features that Operations is concerned about.

Now, the last item is what the Business cares about and that is high availability.

> **ACTION: HA**
> - Exec into primary pod
> - Remove /pgdata/pg14* 
> - Show sim app impact during failover
> - Show how pod is rebuilt

```
kubectl exec -it -c database -n crunchy $(kubectl get pod --selector="postgres-operator.crunchydata.com/cluster=hippo,postgres-operator.crunchydata.com/role=master" -o name -n crunchy) -- bash
cd /pgdata
ls pg14
rm -rf pg14*
ls
```

## Slide 8: Summary
With the built in knowledge of two domains, the Operator:
-   **simplifies** running Postgres in Kubernetes
-   **automates** several aspects of the process
-   builds a **highly available** postgres cluster for the applications

To get started, go to the Crunchy Data examples repository.  There you can deploy the Operator using helm or kustomize and follow some of the examples to deploy your first postgres cluster.

**QUESTIONS**

