# Day 3

## Installing Metal LB

### Procedure

i looked up for metal lb installation guide through github, the official github page for metal lb project redirected me to metal lb official website. apparently metal lb can be installed by applying it's manifest to kubernetes cluster so i did that, i couldn't applied the manifest from github to my cluster though so i copied the content of the manifest, created a metal lb manifest and pasted it. applied metal lb manifest and it got installed. lastly it was time for ip address pool for metal lb so it can assign ip addresses to my applications on cluster. i created a ip address pool manifest for it and used layer 2 advertisement method to advertise that ip outside of cluster network.

in the next 2 days, i will be installing ingress controller and writing ingress to fully implement traffic redirecting from outside of cluster through application pods. completing this puzzle.