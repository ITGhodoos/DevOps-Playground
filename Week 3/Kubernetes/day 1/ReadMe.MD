# Day 1

## create role & rolebinding, cluster role & cluster rolebinding for cluster user connecting to cluster from wsl 2

### PROCEDURE

first i created users related to scenario on wsl 2. then created a directory for RBAC manifests on my master node; reviewed the manifest structure for role, rolebinding, clusterrole, clusterrolebinding; and start writing down the manifests. cluster-user should have read access to all related kubernetes resources used in future and previous scenarios. so i included them in the manifests. then i studied the manifest templates further; i soon found out some non namespace true resources better be mentioned in cluster role. so i created cluster role for my cluster-user and included the resources there.
theses resources are:
- namespace
- nodes
- secrets
- persistentvolumes

which were previously written in role manifest under "apiGroups: [""]".

finally i applied tested the functionality of RBAC resources by:

```bash
root@k8s-master:~/k8s-playground/RBAC# kubectl auth can-i get pods -n k8s-playground --as=cluster-user
yes
root@k8s-master:~/k8s-playground/RBAC# kubectl auth can-i get nodes -n  --as=cluster-user
Warning: resource 'nodes' is not namespace scoped

yes
```
### Achievements
- learnt about functionality and difference between role & cluster role
- learnt about different api groups category in kubernetes
- learnt cluster-scoped resources should be mentioned in cluster role instead of role