# Scaling

As the number of pods and nodes grow, you will want to add more or remove node/s to handle the load. The process is going to remain the same for both master or worker nodes.

## Decommissioning worker node

`Steps:`

## Validate cluster

```sh
kops validate cluster --name=demok8scluster.k8s.local
```

## List available nodes

```sh
kubectl get nodes -o wide
```

## Get cluster

```sh
kops get cluster
```

## Update the cluster

Let's say our current number of worker nodes are two, and we want to remove one worker node. Basically, all the nodes whether they are master or worker nodes they'll be in an auto-scaling group, so we need to edit the auto-scaling group which is the instance groups, and set the MINSIZE and MAXSIZE as per your desire. So, let's do that:

`Display one or many instance group resources`.

```sh
# Usage
# kops get instancegroups [INSTANCE_GROUP]... [flags]

# Get all instance groups in a state store
kops get instancegroups

# Get a cluster's instancegroup
# kops get instancegroups --name <CLUSTER_NAME> <INSTANCE_GROUP_NAME>
kops get instancegroups --name demok8scluster.k8s.local nodes

# Save a cluster's instancegroups desired configuration to YAML file
kops get instancegroups --name demok8scluster.k8s.local -o yaml > instancegroups-desired-config.yaml
```

The above command list the instance groups details.

`Note: We're not editing the cluster configuration but we're editing the instance groups configuration`.

## Edit an instance group desired configuration.

```sh
# Usage
# kops edit instancegroup INSTANCE_GROUP [flags]

# kops edit instancegroup --name <CLUSTER_NAME> <INSTANCE_GROUP_NAME> <STATE_STORE>
kops edit instancegroup --name demok8scluster.k8s.local nodes --state=s3://kops-ashu-storage

# Global Flags:
#      --config string   yaml config file (default is $HOME/.kops.yaml)
#      --name string     Name of cluster. Overrides KOPS_CLUSTER_NAME environment variable
#      --state string    Location of state storage (kops 'config' file). Overrides KOPS_STATE_STORE environment variable
#  -v, --v Level         number for the log level verbosity
```

> This opens your editor (as defined) and allows you to edit the configuration minSize: maxSize:, and automatically updated when we save and exit the editor.

## Update the cluster

```sh
kops update cluster --name demok8scluster.k8s.local --yes
```

```sh
kops rolling-update cluster --name demok8scluster.k8s.local --yes
```

## Validate the cluster

```sh
kops validate cluster --name demok8scluster.k8s.local
```

```sh
kubectl get nodes -o wide
```

We've successfully decommissioned the worker node.

## Upscaling Master node

`Steps:`

## Get instance groups (alias instancegroups/ig)

```sh
# kops get ig --name demok8scluster.k8s.local
kops get instancegroups --name demok8scluster.k8s.local
```

## Edit instancegroup

```sh
# kops edit instancegroup --name <CLUSTER_NAME> <INSTANCE_GROUP_NAME> <STATE_STORE>
kops edit ig --name <ig-name>
kops edit instancegroup --name demok8scluster.k8s.local <master_instance_group>
```

> This opens your editor (as defined) and allows you to edit the configuration minSize: maxSize:, and automatically updated when we save and exit the editor.

## Update the cluster

```sh
kops update cluster --name ${KOPS_CLUSTER_NAME} --state ${KOPS_STATE_STORE} --yes --admin
kops update cluster --name demok8scluster.k8s.local --yes
```

## Validate cluster

```sh
kops validate cluster --name demok8scluster.k8s.local
```

## Delete cluster

```sh
kops delete instancegroup demok8scluster.k8s.local --name ${KOPS_CLUSTER_NAME} --yes
```

## List available nodes

```sh
kubectl get nodes -o wide
```

## Get cluster

```sh
kops get cluster
```

We've successfully upscalled the master node.
