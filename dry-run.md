Task 1
*******************************************************************************************************************************************************
Create a new ClusterRole named deployment-clusterrole, which only allows to create the following resource types:
✑ Deployment
✑ Stateful Set
✑ DaemonSet
Create a new ServiceAccount named cicd-token in the existing namespace app-team1.
Bind the new ClusterRole deployment-clusterrole to the new ServiceAccount cicd-token, limited to the namespace app-team1.

````
kubectl create clusterrole deployment-clusterrole --verb=create --resource=Deployments,Stateful Set,DaemonSet
kubectl create sa cicd-token -n app-team1
kubectl create clusterrolebinding deploy-b --clusterrole=deployment-clusterrole  --serviceaccount=app1-team1:cicd-token
````
**********************************************************************************************************************************************************
