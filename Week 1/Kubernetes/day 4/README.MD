# Day 4

## Adding Replicas and Revisioning to Previous Deployment Manifest

### Procedure

the process was simple, i just added the `replicas` key below container spec key and applied the manifest.
```bash
NAME                                   READY   STATUS              RESTARTS   AGE     IP           NODE          NOMINATED NODE   READINESS GATES
frontend-deployment-7b98c75655-bwdhf   0/1     ContainerCreating   0          11s     <none>       k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-dxkbx    1/1     Running             0          6m10s   10.244.1.9   k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-f4fz9    1/1     Running             0          6m10s   10.244.2.8   k8s-worker2   <none>           <none>
root@k8s-master:~/k8s-playground# kubectl get po -o wide -n k8s-playground 
NAME                                   READY   STATUS        RESTARTS   AGE     IP            NODE          NOMINATED NODE   READINESS GATES
frontend-deployment-7b98c75655-5q8mt   0/1     Pending       0          1s      <none>        k8s-worker2   <none>           <none>
frontend-deployment-7b98c75655-bwdhf   1/1     Running       0          14s     10.244.1.10   k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-dxkbx    1/1     Terminating   0          6m13s   10.244.1.9    k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-f4fz9    1/1     Running       0          6m13s   10.244.2.8    k8s-worker2   <none>           <none>
root@k8s-master:~/k8s-playground# kubectl get po -o wide -n k8s-playground 
NAME                                   READY   STATUS              RESTARTS   AGE     IP            NODE          NOMINATED NODE   READINESS GATES
frontend-deployment-7b98c75655-5q8mt   0/1     ContainerCreating   0          3s      <none>        k8s-worker2   <none>           <none>
frontend-deployment-7b98c75655-bwdhf   1/1     Running             0          16s     10.244.1.10   k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-dxkbx    1/1     Terminating         0          6m15s   10.244.1.9    k8s-worker1   <none>           <none>
frontend-deployment-f5d95b75f-f4fz9    1/1     Running             0          6m15s   10.244.2.8    k8s-worker2   <none>           <none>
root@k8s-master:~/k8s-playground# nano deployment.yaml
root@k8s-master:~/k8s-playground# kubectl get po -o wide -n k8s-playground 
NAME                                   READY   STATUS    RESTARTS   AGE     IP            NODE          NOMINATED NODE   READINESS GATES
frontend-deployment-7b98c75655-5q8mt   1/1     Running   0          9m23s   10.244.2.9    k8s-worker2   <none>           <none>
frontend-deployment-7b98c75655-bwdhf   1/1     Running   0          9m36s   10.244.1.10   k8s-worker1   <none>           <none>
```
as you can see with changes i made to the manifest, new pods started being created and replaced the older pods from the old manifest, based of kubernetes default update strategy, which is 25% on max unavailable and 25% on max surge. for instance, on a deployment with 4 replicas, it will terminate 1 pod and create 1 pod at the sametime.

after i was done with replicas, it was time to try kubernetes revisioning, basically a manual version control for workloads. i added annotations to my manifest, but it can be achieved using `kubectl` with live objects. after adding annotations i changed the nginx image version and wrote the description into my annotations; applied the newly edited manifest and tested it with `kubectl rollout history` command.
```bash
root@k8s-master:~/k8s-playground# kubectl rollout history deployment -n k8s-playground 
deployment.apps/frontend-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         changed nginx image from alpine to 1.22
```