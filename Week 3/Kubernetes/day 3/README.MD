# Day 3

## giving access to a python app attempting to show pods in cluster via service account

### PROCEDURE

first things first, determining permissions for apps trying to contact the cluster, so i wrote down role and rolebinding manifests.
then it was time for secret. i used a template for this one. then i wrote a service account for my app; applied the manifests and:

### ERROR: {'kind': 'Status', 'apiVersion': 'v1', 'metadata': {}, 'status': 'Failure', 'message': 'pods is forbidden: User "system:serviceaccount:k8s-playground:app-service-account" cannot list resource "pods" in API group "" in the namespace "k8s-playground"', 'reason': 'Forbidden', 'details': {'kind': 'pods'}, 'code': 403}

there was something on authorization part; giving permissions for app to do it's job. i checked the role and rolebinding manifests, found nothing; checked the service account manifest and realized there is a mismatch between rolebinding subject.name and service account name; fixed it and it did work properly.
```bash
cluster-admin@WonderSweety:~/k8s-playground$ python3 app.py
{'kind': 'PodList', 'apiVersion': 'v1', 'metadata': {'resourceVersion': '285029'}, 'items': []}
```

at the time i had no pods running on k8s-playground namespace so the output was empty as shown.

### Achievements:

this practice scenario mainly helped me with my troubleshooting skills, i can see that my manifest writing has got improved and when it comes to troubleshooting, if something come up and dont work properly, i feel like i know where to look for now.

