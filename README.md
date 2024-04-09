Steps to deploy OpenShift gitops (ArgoCD)

Prereqs: 

requires the oc, kustomize, helm, and kubectl CLIs

Additionally in order for argoCD to deploy to multiple namespaces, we will require cluster-admin or near equivalent permissions. Otherwise ArgoCD will be namespace scoped, and you will need to configure RBAC appropriately

Get Permissions

Clone This locally

Open argoCD folder → cd argoCD

Authenticate against the openshift cluster at the CLI level [IMG]

Once you're authenticated and you're in the argoCD directory you can run → oc apply -k . 

to apply all manifests in the folder using kustomize to the cluster

This will create 2 namespaces, deploy our operator, and our initial argoCD instance to the cluster

Wait for ArgoCD to finish deploying, you can check deployment status by reviewing the status in the argoCD CRD as followed → oc get argocd [img show completed web gui]

this will return the name of our argocd instances, we only need the one in our namespace

then we can check the status by doing a describe on the crd → oc describe argocd/<name-of-instance>

Verify Status: Phase: Availabe [command /img]

ArgoCD is ready and deployed

(optional for cluster-scoped resources) once the operator is done installing, you'll need to ensure argoCD has cluster-admin privileges for non-namespace scoped deployments → oc project <namespace-of-argocd>

this will switch us to the correct namespace of argocd, then we need to verify our serviceAccount for argoCD has cluster-admin privileges. This can be done by doing the following → oc get sa

You'll need the application-controller serviceaccount name so it might look like → <instanceName-argocd-appplication-controller>

we will need to grant the application controller cluster-admin privileges in order for it to view and manage cluster-scoped resources. We can do that with the following command → oc adm policy add-cluster-role-to-user cluster-admin -z <serviceAccount-name>

SUCCESS: Openshift gitops should now successfully be deployed in your cluster in the amespce you deignated 

Next steps: 

configure and deploy a product from another repo ie see repos [link to org] for config, to deloy an actual app we will use:

for example:

```- target: kind: Namespace version: v1 name: argocd patch: |- - op: add path: /metadata/name value: gitops



If you'd like to change the namespace from gitops to something else, replace the value: gitops

Commands

Actions

cd argoCD

open folder

oc apply -k . 

apply manifests

oc get argocd

view names of instances

oc describe argocd/<name-of-instance>

check status

oc project <namespace-of-argocd>

switch to namespace

oc get sa

view service accounts in namespace

oc adm policy add-cluster-role-to-user cluster-admin -z <serviceAccount-name>

grant cluster admin privileges

References

https://medium.com/@nanditasahu031/getting-started-with-argocd-b5a02353e144

https://www.cncf.io/projects/argo/

