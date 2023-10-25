---
title: "Grafana_integration"
date: 2023-10-23T22:16:31+02:00
draft: false
weight: 402
---


Integrate Grafana into ACM

- Login to the bastion host host.
- Create a namespace by running the following command: 
  ```oc create namespace open-cluster-management-observability```

Copy the pull secret into this new namespace by running the following TWO
commands:

```DOCKER_CONFIG_JSON=`oc extract secret/pull-secret -n openshift-config
--to=-```


```oc create secret generic multiclusterhub-operator-pull-secret -n
open-cluster-management-observability
--from-literal=.dockerconfigjson="$DOCKER_CONFIG_JSON"
--type=kubernetes.io/dockerconfigjson
```

- In your current folder create a file called thanos-object-storage.yaml and add the
following text in the file. Please be sure to update your S3 bucket name and AWS
Keys

```
apiVersion: v1
kind: Secret
metadata:
name: thanos-object-storage
type: Opaque
stringData:
thanos.yaml: |
type: s3
config:
bucket: YOUR_S3_BUCKET
endpoint: s3.amazonaws.com
insecure: false
access_key: YOUR_ACCESS_KEY
secret_key: YOUR_SECRET_KEY
```

- Create a secret for your object storage by running the following command:

```oc create -f thanos-object-storage.yaml -n open-cluster-management-observability```

- Create the MultiClusterObservability custom resource for your managed clusters. To do
this create a YAML file named multiclusterobservability_cr.yaml

```kind: MultiClusterObservability
apiVersion: observability.open-cluster-management.io/v1beta2
metadata:
name: observability
spec:
observabilityAddonSpec: {}
storageConfig:
metricObjectStorage:
key: thanos.yaml
name: thanos-object-storage```


Note: This is the default operation. There are multiple other optional fields to customize this resource. If
you want to change optional parameters like retentionResolution, storageClass to use, etc., please check
the API reference.


- Apply the observability YAML to your cluster by running the following command:

```oc apply -f multiclusterobservability_cr.yaml```

- Log in to the ACM console, and navigate to Observe environments -> Overview.

- Click on the Grafana link in the top right to view the metrics from the managed clusters.
** Please note:** it will take a few minutes for the metrics to become visible on the
dashboard.

![Grafana Cluster](/401_1.png)