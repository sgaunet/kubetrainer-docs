
This project will try to guide you to get a local kubernetes environment in order to:

* learn
* try
* break
* retry
* ...


This project is tested under Linux and macOS (Docker Desktop).

* You have to install docker
* and [kind](https://kind.sigs.k8s.io/)
* and [task](https://taskfile.dev/)

## Platform Notes

- **Linux:** All tutorials work with direct localhost access
- **Mac/Windows (Docker Desktop):** Tutorials work as documented. The Ingress tutorial (04-INGRESS) requires `kubectl port-forward` for access due to Docker Desktop's VM networking

Done ?

Ok, let's begin by creating the cluster.

## Create the kubernetes cluster

```bash
$ cd DOCS/00-KIND
$ task create-cluster
task: [create-cluster] kind create cluster --config kind-config.yaml
Creating cluster "kind" ...
...
```

## Connect to the cluster

```bash
$ kubectl cluster-info --context kind-kind  # execute this command to be able to contact the kubernetes cluster
```

What should I do now ?

Follow tutorials in the [DOCS](DOCS) directory.
