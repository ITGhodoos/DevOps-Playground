# Day 5

## installing nginx ingress controller & exposing nginx application to outside network

### PROCEDURE

after checking nginx official online installation guide, i decided to learn helm and continue the installation procedure using helm. i installed helm, added bitnami repo and play a little with helm to get more familiar with it. but when it came to installing ingress controller,unfortunately i encountered imagepullbackoff error when deploying its pods on my cluster due to access restricitions for iran. not to mention i used my tun2socks solution for bypassing bitnami's access restriciton too; but i was afraid if using tun2socks directing all my traffics to tunnel interface, could cause network issues when calling kubernetes api server. so i switch helm installation for manual installation using manifests. at first i encountered imagepull error again, i checked the manifests for imagePullPolicy and decided to manually pull nginx ingress image by containerd:

```crictl pull nginx/nginx-ingress:5.7.1```

once i got the image i restarted previously created pods, the image pull backoff was gone but now pods were crashing for some other reason.

### ERROR: ingressclasses.networking.k8s.io No resources found

this error was obvious and fairly simple to troubleshoot, because i did this whole scenario in 3 days, i forgot to apply ingress and ingress class manifests prior to my ingress controller installation. with applying ingress class manifest, something else came up.

### ERROR: nginx has an invalid Spec.Controller k8s.io/ingress-nginx; expected nginx.org/ingress-controller

when i was writing my ingress class name, i used chatGPT to show me ingress class name manifest template so i could write my own. i used a wrong controller value for my nginx ingress; well not me, the AI

### ERROR: The IngressClass "nginx" is invalid: spec.controller: Invalid value: "nginx.org/ingress-controller": field is immutable

ingress class name manifest cannot be reloaded with new configuration by ```apply -f``` command, you have to delete the previous ingress class name and apply the new one manually.

### ERROR: Error when getting nginx-ingress/nginx-config: configmaps "nginx-config" not found

since i was using nginx official manifests, i didn't realized it got some extra keys and configurations for general use, i trusted the chatGPT solution for this problem and generated an empty config map for my nginx ingress and restarted the pods.

after several errors, i got the pods running but realized my application didn't obtain any external ip addresses, after some looking around and asking AI about it, i realized there are no loadbalancers for my nginx application. so there was nothing to go fetch external ip address from metal lb. i wrote down the manifest, and once i applied it, external ip address appeared in front of my load balancer service;
but there was something still not working properly.
when i used ```curl``` to test my service, it replied with 404; i totally forgot to apply nginx deployment so there was no backend to respond to requests. after applying the deployment, i still got the 404, and realized there are no service running for my deployment; i quickly applied previously created cluster ip service for my nginx and right after that everything got working.

### Achievments:

- learned how to use basic helm tasks
- learned nginx ingress controller installation process
- expanded my understanding on how application exposure to outside network work in kubernetes
- improved troubleshooting skills on kubernetes cluster environment

