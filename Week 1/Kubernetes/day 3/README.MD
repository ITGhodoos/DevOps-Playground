# Day 3

## Creating a Deployment Manifest.

### PROCEDURE

i started with main structure of a kubernetes manifest. `apiversion`, `kind`, `metadata`, `spec`. fill in the manifest as previous scenarios. applied it.
### ERROR: Error from server (BadRequest): error when creating "deployment.yaml": Deployment in version "v1" cannot be handled as a Deployment: strict decoding error: unknown field "spec.containers"

turns out deployment manifests differs in yaml structure. so i looked up some deployment manifests. i learned about `template` and `spec` which is a recipe of pod creation for deployment workloads. deployment will use these 2 to manage number of pods if it needs to create a new one. in  other words if a deployment needs to create a new pod, it will create one matching the specifictaion, specified in `template` and `spec`.

- template states the metadata part of pods for deployment.
- spec indicates the method of picking pods to manage by deployment workload; mainly the `selector` tag which specifies which pods to look up for using pod labels.

deployment uses template to add labels and other metadatas to pod during the pod creation proccess, spec will indicate the pods to lookup, for deployment.
i added these 2 to my manifest, fixed a little type o error in `matchlabel` part and it did got up and running.

### Achievments

- i learned about the manifest structure of deployment workload
- i got a deeper understanding of how deployment works.
- my manifest writing skill improved