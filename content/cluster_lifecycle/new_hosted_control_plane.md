---
title: "Create Hosted Control Plane"
date: 2023-10-23T22:02:41+02:00
draft: false
weight: 3
---

** Hosted Control Planes** is a **technology preview** feature, it shouldn’t be used in production clusters. 

In this section, we will enable the feature and deploy a Hosted Cluster on AWS.

On the Clusters page, select once again Create Cluster.

Select **Amazon Web Services** and **Hosted** mode. You’ll face the official instructions on how to create a hosted cluster.

1 - Turn on Hosted Control Planes functionality

The hosted control planes feature is available as a technology preview item and it’s disabled by default. 
First,have a  look in the email  instructions on how to log in to the bastion machine, and let’s start the setup.

In the bastion VM, run: `aws configure`

When prompted, enter your **AWS credentials** according to the RHDP email you received. For the default region name, use: `us-east-2`. Leave the default output format blank.

Next, let’s create a S3 bucket with public access to host OIDC discovery documents for your hypershift clusters. Run:
```
$ export BUCKET_NAME=hcp-oidc
$ export REGION=us-east-2
$ aws s3api create-bucket --bucket $BUCKET_NAME \
 --create-bucket-configuration LocationConstraint=$REGION \

  --region $REGION

  $ aws s3api delete-public-access-block --bucket $BUCKET_NAME

  $ echo '{

    "Version": "2012-10-17",

    "Statement": [

        {

            "Effect": "Allow",

            "Principal": "*",

            "Action": [

                "s3:PutObject",

                "s3:GetObject"

            ],

            "Resource": "arn:aws:s3:::${BUCKET_NAME}/*"

        }

    ]
  }' | envsubst > policy.json



  $ aws s3api put-bucket-policy --bucket $BUCKET_NAME --policy file://policy.json

```
Now, let’s create a Secret in the local-cluster namespace so the HyperShift operator will know how to use the bucket that we just created. Don’t forget the second command to apply the labels:
```

  $ oc create secret generic hypershift-operator-oidc-provider-s3-credentials --from-file=credentials=$HOME/.aws/credentials --from-literal=bucket=$BUCKET_NAME --from-literal=region=us-east-2 -n local-cluster

  
  $ oc label secret hypershift-operator-oidc-provider-s3-credentials cluster.open-cluster-management.io/credentials="" cluster.open-cluster-management.io/type=awss3 -n local-cluster
```
You can validate it in the ACM UI, just click on Credentials:

![Validate ACM UI](/101_10.png)

Finally, we’re now ready to enable the feature. Let’s do it by patching the **MultiClusterEngine CR**.

```

  $ oc patch mce multiclusterengine --type=merge -p '{"spec":{"overrides":{"components":[{"name":"hypershift-preview","enabled": true}]}}}'
```

Enabling the feature automatically enables the hypershift-addon on local-cluster, per image below. This will also install the HyperShift operator in the hypershift namespace. The installation may take a few minutes to complete.

NOTE: As mentioned, the hypershift add-on will be automatically installed in the local-cluster. Access the Add-ons tab in the Clusters view and you’ll see the degraded status. As you can see in the image below:

This is because the operator will try to deploy 2 operator pods, but this is a single-node OpenShift environment. The anti-affinity rule will make the second pod stay as ‘pending’.
In the OpenShift console, go to the Deployments view in the hypershift project and manually scale it down to 1. This won’t affect the deployment of the HostedCluster, and consequently won’t affect the demonstration. See image below:

You should now see all the add-ons back to the available status in the local-cluster add-ons tab.

### 2 - Deploying the Hosted Cluster

First of all, we’ll need to copy your pull secret from this link.

In the bastion machine, paste the content of your pull secret in a file called pull-secret in the home directory. Example:
```
  $ vim $HOME/pull-secret
```
Next, we’ll need the hypershift CLI. From the OpenShift Container Platform console, in the right-hand side of the top bar, click the Help icon (?) -> Command Line Tools.

Right-click Download hypershift CLI for Linux for x86_64, and click on Copy Link Address or run:
```  
  $ oc get consoleclidownload hypershift-cli-download -o json | jq -r '.spec.links[] | select(.text=="Download hypershift CLI for Linux for x86_64").href'
```
Now, from the bastion machine, paste the link you just got  into the below command and run the following command:
```
  $ wget <the link copied in the step above>
```
Example: $ wget https://hypershift-cli-download-multicluster-engine.example.com/linux/amd64/hypershift.tar.gz

You should see a new file named hypershift.tar.gz in the lab-user home directory. Now, run:
``````
  $ tar xvzf hypershift.tar.gz

  $ chmod +x ./hypershift

  $ sudo mv ./hypershift /usr/local/bin/
``````

Finally, let’s create a namespace to host our HostedCluster and NodePools objects.
```

  $ oc new-project hcp-clusters

```
Export the following variables:
```

  $ export CLUSTER_NAME=hcp
  
  export INFRA_ID=hcp
  export NAMESPACE=hcp-clusters

  export BASE_DOMAIN=<<replace here with your base domain from the RHDP email>>
```
And run:
```
  $ hypershift create cluster aws \

  --name $CLUSTER_NAME \

  --infra-id $INFRA_ID \

  --namespace $NAMESPACE \

  --base-domain $BASE_DOMAIN \

  --pull-secret $HOME/pull-secret \
  
  --node-pool-replicas=3 \

  --aws-creds $HOME/.aws/credentials \
  
  --region $REGION \

  --release-image quay.io/openshift-release-dev/ocp-release:4.13.12-x86_64
```
NOTE: The command itself might finish quickly, and soon, you’ll see the cluster listed in the Clusters page, but the whole installation process takes around 15 minutes to complete.

Notice that it says “Pending import”. That’s ok for now but you’ll need to manually import the cluster once the installation is finished. Now, click on the cluster entry (hcp) to display some details, information and outputs to verify the installation progress.

After a little while, once you see all the configs are reporting ready  ✅ - all Cluster Operators are successfully deployed, ClusterVersion is available, NodePool is ready - go ahead to the bottom of the page and you should be able to access the Console URL with the provided kubeadmin credentials.

Note: some failing status may be also acceptable depending on the hosted cluster configuration. For example: for ClusterVersionUpgradeable, showing a ❓red icon could be normal depending if the next version available has API deprecations.

![Hypershift Add-On](/101_11.png)

Don’t forget to import the cluster in the cluster’s view, so RHACM will install its agent in the Hosted Cluster. If everything went successfully, at the end, should see something like this:

Bonus: you can see the containerized control plane by accessing the Topology page in the Developer view and selecting the namespace hcp-clusters-hcp.


