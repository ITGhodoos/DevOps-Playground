# Day 2

## Creating a Pod Manifest which include initcontainer

the goal is to create a pod which includes an init container; init container make's the main container wait for database pod to be up and running, once database pod is ready for service it will lunch the main container.

### Procedure

i created a new manifest file, added the essential key values just as previous scenario. i got realized that `initContainers` key must be added under `containers` key. my original plan was that database container be part of my pod but i realized that if i do that it won't work that way. basically for my scenario, database container should be already up and running if i want my init container to test if database pod is servicable or not. if i added database container to my pod manifest which includes my init container it will never get created since containers in a pod will only get created after init container is done doing it's task.
so i created a database pod manifest to solve this dependency; applied it and wrote the ip address of the created pod down to my init container pod manifest. i wondered since containers in a pod and a namespace are able to use shared resources of each other, wether they can call each other by container name; turns out they ***can't***. kubernetes wont assign a FQDN for pods so you can't use pod names instead of IP Addresses for network operations. during all of this, i soon realized i know very little about yaml language so i feel like i need to learn more about it so i'd be able to write manifests much more easier. 
i used this yaml tutorial on youtube:

[https://www.youtube.com/watch?v=1uFVr15xDGg](URL)

it really helped me with yaml syntax. now back to our main subject
i applied pod and init-container-pod manifest, pod manifest did work and got running, i tested the connection state with `nc` command and it was successful. however init-container pod didn't want to get running, i used `kubectl describe` and `kubectl logs` command to troubleshoot and i knew that there was something with the network connection, putting pod name instead of pod ip address seemed wrong by my network instincts.
so i changed the name to pod ip address delete the previous instance and applied the manifest again, everything got up and running.

### Achievments

- i got more comfortable with kubernetes manifest writing
- i learned the mechanism of deploying manifests with init containers
- i learned yaml syntax and how to use it properly
- i learned crucial points about kubernetes networking mechanism
- i learned the purpose of busybox container and what can it basically do
- i learned the difference between `kubectl logs` and `kubectl describe` command and their usecases

