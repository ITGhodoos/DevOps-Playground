# Day 1

## Cluster IP Service

plan was to write a cluster ip service manifest so that i get more familiar with its functionality and manifest structure.

### PROCEDURE

i studied service manifests basic structure. wrote my clusterip manifest and applied it with my previously created deployment workload. checked if i can `curl` my nginx through cluster ip address. it couldn't resolve.

### ERROR curl: (7) Failed to connect to 10.110.209.83 port 80 after 0 ms: Could not connect to server

i used `kubectl get ep` command to see if pod ip addresses are added to my cluster ip endpoint.it was empty. i double checked my service and deployment manifests and found out that there is a mismatch between pod labels and service selector labels; fixed it and reapplied everything. and it worked.

### NOTE

service selector labels should match pod labels not the workload labels, and if service selector labels doesn't include all the pod labels it still should technically work.