---
title: "Create_s3_bucket"
date: 2023-10-23T22:13:35+02:00
draft: false
---

End to End Visibility (Observability use case)
View system alerts, critical application metrics, and overall system health. Search, identify, and
resolve issues that are impacting distributed workloads using an operational dashboard
designed for Site Reliability Engineers (SREs). This is done via the integration of Grafana.
Let's walk through the steps to integrate Grafana with ACM.
You will need your AWS Keys. (from RHDP system email)
You will also need to create an AWS S3 bucket.
SSH information to your bastion host. (from RHDP system email)
Create the S3 bucket

- Login to the bastion host.
- Run the following command to login to AWS: 
  `aws configure`
  and enter your AWS keys when prompted. Default region: `us-east-2`

Then run the following command to create the S3 bucket: 
`aws s3 mb s3://grafana-$GUID`

Please take note of the bucket name.

Integrate Grafana into ACM

- Login to the bastion host host.
- Create a namespace by running the following command: 
  `oc create namespace open-cluster-management-observability`

Copy the pull secret into this new namespace by running the following TWO
commands:

`DOCKER_CONFIG_JSON="oc extract secret/pull-secret -n openshift-config --to=-"`

`oc create secret generic multiclusterhub-operator-pull-secret -n open-cluster-management-observability --from-literal=.dockerconfigjson="$DOCKER_CONFIG_JSON" --type=kubernetes.io/dockerconfigjson`

In your current folder create a file called thanos-object-storage.yaml and add the
following text in the file. Please be sure to update your S3 bucket name and AWS
Keys

```apiVersion: v1
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
secret_key: YOUR_SECRET_KEY```


- Create a secret for your object storage by running the following command:
```oc create -f thanos-object-storage.yaml -n open-cluster-management-observability```

Create the MultiClusterObservability custom resource for your managed clusters. 
To do this create a YAML file named "multiclusterobservability_cr.yaml"


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