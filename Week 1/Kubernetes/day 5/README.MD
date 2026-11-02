# Day 5

## Adding Probes To Deployment

### Procedure

before i make any changes i had to come up with a proper probe for my nginx deployment. first i decided to use liveness probe which pings the pod ip.
i made the changes to deployment. encountered a minor syntax error on my yaml file, fixed it and applied the manifest. at first pods got created but after like 5 minutes, they became "crashloopbackoff", i looked at container pods, the nginx was started successfully but exited after 5 minutes. so my nginx was healthy, it was the liveness probe; as it turned out i had to put a `-c` after my ping command. without it the ping command will run simoultaneously and endless so it will never exit and never complete the liveness probe procedure. i added `-c` applied the manifest and fixed it. using `kubectl describe` command i could see that pod turned to ready state which previously wasn't.

### Achievements
this scenario was helpful for me to understand the HA concepts of a kubernetes cluster such as canary updates & purpose of probes and how they work.
- i learned that probes are tests that check the health state of applications and containers and they not just do it at initial stage of pod creation but during the container life cycle.
- i learned how to fetch data of pod using `valueFrom` key.
- i got familiar with probes syntax.

