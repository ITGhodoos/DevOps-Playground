# Day 5

## Adding Probes To Deployment

### Procedure

before i make any changes i had to come up with a proper probe for my nginx deployment. first i decided to use liveness probe which pings the pod ip.
i made the changes to deployment. encountered a minor syntax error on my yaml file, fixed it and applied the manifest. at first pods got created but after like 5 minutes, they became "crashloopbackoff", as it turned out i had to put a `-c` after my ping command. without it the ping command will run simoultaneously and endless so it will never exit and never complete the liveness probe procedure. i added `c` applied the manifest and fixed it. using `kubectl describe` command i could see that pod tuned to ready state which previously wasn't.

