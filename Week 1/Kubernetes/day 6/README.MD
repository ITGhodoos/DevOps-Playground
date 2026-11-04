Day 6

## Rewriting default Update Strategy & implementing HPA

### Procedure

i added the update strategy and rolling update keys to my deployment workload, after a lot of syntax errors and failed attempts i finally got it to run, after that since i was yet to get comfortable with service manifest i asked ai to write a proper service manifest for my scenario. and lastly it was time for hpa. i wrote the hpa and applied all manifests to my k8s-playground namespace.
after that i needed something to test the pod scaling functionality. i decided to create a pod based on busybox that would send http requests to my deployment. before i run the load-generator pod i decided to check the deployment myself so i used `curl` to FQDN of my service. it didn't work at the beginning but because that i did run it on my master node, kubernetes coredns only work on scale of kubernetes'es resources, meaning only pods and other resources can use it.
so i used `kubectl exec` to check the response of my deployment through my pods. it responded successfully. after that i run the load-generator pod.
i realized no additional pods got created after i run my load-generator pod. so i look around for the reason. i learned that you have to specify the initial amount of resources for pods on your workload manifest for scaling to work. otherwise it won't i added requests and limits to my deployment and restarted everything. still nothing. after more looking around i realized for scaling to work it needs something to monitor the usage of pods and report it back to hpa system. i tried to use metric-server but due to access restrictions i couldn't pull the image of it. i aborted the scenario since it was taking too long to make. i will come back to this scenario once i learn prometheus and will try it with promeus metric server.

### Achievements:

although i failed to complete the scenario, it really helped me with these:

- my troubleshooting scales when it comes to kubernetes cluster.
- my knowledge and understanding on kubernetes core components and their behaviour such as coredns.
- kubernetes scaling mechanism
- my manifest writing skills