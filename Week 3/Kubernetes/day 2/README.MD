# Day 2

## Creating Cluster Role & Cluster Role Binding admin and remote access for admin & user to Cluster

### PROCEDURE

i wanted a remote admin access to cluster so i can manage it remotely via wsl 2 instead of direct ssh to master node. so i started with cluster role and cluster role binding manifest for my cluster-admin user. when i was done with manifest writing, i generated crt files for cluster-admin and cluster-user on my wsl 2
i followed these 3 simple steps/commands:

1. ```openssl genrsa -out <name>.key 2048```
2. ```openssl req -new -key <name>.key -out <name>.csr -subj "/CN=<username>"```
3. ```openssl x509 -req -in <name>.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out <name>.crt -days 365```

### NOTE: if kubernetes is installed via kubeadm, the CA Directory will be "/etc/kubernetes/pki/"; otherwise it might be different

i generated the crt files for both users now everything was settled for creating kubeconfig file.
i asked ChatGPT for config template and replaced the values with each user's data. once it was done i created a duplicate for config file in wsl 2 machine on each user's home directory; after i created .kube directory for each of them there. i had kubectl installed from before so i didnt got bother with it. remote access for cluster-admin user worked flawlessly; however on cluster-user things got a little not smooth.

### ERROR: nodes is forbidden: User "cluster-user" cannot list resource "nodes" in API group "" at the cluster scope

this was awkward for me because i created a cluster role & binding for my cluster-user so it can have access to cluster-scoped resources. i looked up at my clusterrole & clusterrolebinding manifests and soon realized the problem. the subject section for clusterrolebinding was wrongly pointing at cluster-access group instead of cluster-user user; so i changed the "subject.kind" value from group to User and "subject.name" to cluster-user; problem solved.