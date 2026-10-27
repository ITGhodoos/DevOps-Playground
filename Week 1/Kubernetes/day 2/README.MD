# Day 2

## Creating a Pod Manifest which include initcontainer

the goal is to create a pod which includes an init container; init container make's the main container wait for database pod to be up and running, once database pod is ready for service it will lunch the main container.

### Procedure

i created a new manifest file, added the essential key values just as previous scenario. i got realized that `initContainers` key must be added under `containers` key. my original plan was that database container be part of my pod but i realized that if i do that it won't work that way. basically for my scenario, database container should be already up and running if i want my init container to test if database pod is servicable or not. if i added database container to my pod manifest which includes my init container it will never get created since containers in a pod will only get created after init container is done doing it's task.
so i created a database pod manifest to solve this dependency; applied it and wrote the ip address of the created pod down to my init container pod manifest. i wondered since containers in a pod and a namespace are able to use shared resources of each other, wether they can call each other by container name; turns out they can. instead of pod ip address of the database pod i could use database pod name to check the status of my database pod. and it would be smart, because if pod gets recreated for any reason, it will probably be obtaining a new ip address from kubernetes.

