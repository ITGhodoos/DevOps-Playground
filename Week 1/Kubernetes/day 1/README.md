# Day 1

## Creating a Pod manifest and deploy it on K8S cluster

the goal was to write a pod manifest, deploy it on the cluster and test the functionality of the pod.

## PROCEDURE

i started with writing down my manifest; i knew that there are 4 main key-values to every kubernetes manifest; which are:

- apiVersion
- kind
- metadata
- spec

so i wrote them down firstly. i used `screen` command for my text editor, to make the flow of editing and checking things on my server much more simoultaneous.
after i done the main key values of my manifest, it was time for subkeys, i mean the key values under the main key values. so i added the essential ones such as `image:`. then i looked up at other subkey values i can add and their purposes. i learned that `app.kubernetes.io/component:` is used to indicate what part does your pod take. i used "frontend" value for my manifest. the other keys i learned are such as:

- app: "app name"		
- app.kubernetes.io/name: "resource/pod name"	
- app.kubernetes.io/instance: "id of the instance of application"		
- app.kubernetes.io/version: "application version"	
- app.kubernetes.io/part-of: "what other bigger structure it is part of"		
- app.kubernetes.io/managed-by: "what manages this resource/pod"

i also learned that you could use custom `label` key values for your manifest.

as i was completing my pod manifest, i encountered `containerPort:` key value.
i came up with the question does this make the pod to expose defined port? i asked ai about it and turn's out, **no**; the main purpose of containerPort is documentation so kubernetes does know it uses port "80" for example to listen to outside requests.

i saved my manifest now it was time to apply it. and it did give me an error:

### ERROR: Error from server (BadRequest): error when creating "pod-manifest.yaml": Pod in version "v1" cannot be handled as a Pod: json: cannot unmarshal object into Go struct field PodSpec.spec.containers of type []v1.Container

as it turned out i had added space between `apiVersion` and `kind` and `metada` to make them look seperated. kubernetes apparantly does'nt like that.
so i remove the space between main key values and again it did come with a new error:

### ERROR: error: error parsing pod-manifest.yaml: error converting YAML to JSON: yaml: line 11: did not find expected '-' indicator

this time it was simple i had the indentation wrong for container segment. it was a quick fix. and here we go again:

### ERROR: Error from server (NotFound): error when creating "pod-manifest.yaml": namespaces "k8s-playground" not found

i thought when you type `namespace` key value, it will automatically create that namespace for you, turn's out it doesn't. so i created the namespace applied the manifest again.

**success**

now that the pod was running it was time to test the functionality of it. i used `curl` command to see if nginx web page appears on terminal or not. first i used the pod ip address to do it and it worked. second time i wonder what if i used node ip address; it won't work. and it's natural due to how networking works. basically it's the pod that is exposing it's defined port to outside not the node, for that purpose you have to use services prefferrably nodeport for this usecase.

## achievments

this is what i learned and improve doing this practice scenario:

- i got much more comfortable with manifests
- i learned much more about purpose of each key value on a manifest
- i got more thoughtful and precise to use what kubectl command for each stage of troubleshooting
- my knowledge on how kubernetes networking work's got a little deeper
- i used `screen` command to make my self more comfortable with these type of tools in linux