---
icon: home
label: Onepager
---

## [1] Deploying Weaviate to AWS EKS: High-Level Guide

In this high-level guide you will learn how to deploy Weaviate to AWS managed Kubernetes service. The guide consists of the following steps:
- Initial AWS access configuration
- Provisioning AWS cluster and nodes
- Deploying the Weaviate cluster

### Prerequisites

!!! warning Assumption
This guide mostly uses CLI to deploy and operate your resources so you are going to need a few tools present in your system:
- [AWS CLI](https://aws.amazon.com/cli/) - official AWS command-line-interface that makes your AWS resources accessible from your local console.
- [kubectl](https://kubernetes.io/docs/tasks/tools/) - command-line-interface used to interact with your cluster.
- [helm](https://helm.sh/docs/intro/install/) - package manager used to manage your Kubernetes applications.

- You also need access to an AWS account where you can deploy a cluster
!!!

### AWS environment setup

To access AWS from your local environment you need to create [access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in 
the AWS console.
These consist of **Access Key ID** and **Secret Access Key**

!!!warning Best Practice
When deploying a long-term production applications, it is recommended to use temporary security credentials (such as IAM roles) 
instead of creating long-term credentials like access keys.
!!!

With the keys to access your resources, you can connect your local environment to AWS.
Take care to use your correct keys and your preferred region, we use Europe as an example.

```
aws configure
AWS Access Key ID [None]: AKIA4S***KLPS4X
AWS Secret Access Key [None]: yV1xYwC***DkwDESP7
Default region name [None]: eu-central-1
Default output format [None]: json
```

You may also need to verify the identity of your connection in your console:

```
aws sts get-session-token
aws sts get-caller-identity
```

!!!todo
- explain creating and attaching correct IAM roles to cluster/nodes
!!!

### Create an AWS cluster

Now you are ready to create the AWS cluster and provision nodes where Weaviate will run.

We are creating a cluster called `test-weaviate-cluster` in the `eu-central-1` region with the default arn role and permissions `AmazonEKSAutoClusterRole`.
The cluster has a vpc configured, with 3 subnets and a security group. 

```
aws eks create-cluster \
    --region eu-central-1 \
    --name test-weaviate-cluster \
    --role-arn arn:aws:iam::864899855787:role/AmazonEKSAutoClusterRole \
    --resources-vpc-config subnetIds=subnet-097e54198b9672925,subnet-052f6901421eac970,subnet-07504c6004065e0a6,securityGroupIds=sg-05fa3c9ea74b9afba
```

!!! todo
- explain the correct configuration of the subnets and security group
- explain how to switch local context to the aws environment
!!!

#### Addons

Considering our use case, there are a few AWS Addons that should be used:

```
aws eks create-addon \
    --region eu-central-1 \
    --cluster-name test-weaviate-cluster \
    --addon-name coredns
```

Add the rest of the addons in a similar fashion:
- CoreDNS: Required for internal service discovery.
- kube-proxy: Manages networking rules for communication between Kubernetes services.
- Amazon VPC CNI: Handles pod networking.
- Amazon EBS CSI Driver: Necessary for dynamic storage provisioning, critical for database workloads.


### Provisioning nodes

For this guide we use two on-demand small nodes with a modest amount of storage.
- AMI type: Amazon Linux 2
- Instance type: t3.small
- Minimum size: 1
- Maximum & Desired size: 2
- Storage: 20GiB

!!! todo
- mention why it is recommended to use *gp2* or *gp3* EBS volumes.
!!!

### Deploying 

Weaviate can be deployed with the provided [Helm Chart](https://github.com/weaviate/weaviate-helm).

!!!todo
- full exaplanation of the deployment itself. Doing this in the second step-by-step part.
!!!

### Monitoring

!!!todo
- provide an example monitoring stack. Something like Prometheus, Loki, Promtail, Grafana. Prometheus looks to be built-in looking at the helm chart.
- we could also provide an example data Visualization solution. I have used things like Metabase, Grafana of course, Looker Studio.
!!!

## [2] Deploying Weaviate with helm

After configuring your cluster and provisioning adequate resources you can now deploy Weaviate 
using provided [Helm Chart](https://github.com/weaviate/weaviate-helm).

Helm is a package manager tool for Kubernetes that makes it easier to tweak provided package to 
your specific needs and use case using helm charts.

1. Add the Weaviate helm repo:

```
helm repo add weaviate https://weaviate.github.io/weaviate-helm
helm repo update
```

2. Export the default chart into accessible file:

```
helm show values weaviate/weaviate > values.yaml
```

!!! note
The `values.yaml` configuration allows you to customize all the parameters you might need:

- Scaling and Resources: Replicas, Resource requests/limits, Scheduling.
- Networking: Services for HTTP and gRPC with LoadBalancer or ClusterIP.
- Storage: Define persistent volumes, storage class, and available storage.
- Authentication & Authorization: Enable API key-based authentication, OIDC integration, and RBAC for user roles.
- Observability: Configure Prometheus.
- Modules: Enable vectorization, generative AI, multimodal processing, and query-ranking modules.
- Data Management: S3 and other backup solutions.

As an example, for our deployment we changed the size of storage and the type of storage:

```
storage:
  size: 10Gi
  storageClassName: "gp2"
```
!!!

3. Create the weaviate namespace

```
kubectl create namespace weaviate
```

4. Deploy

```
helm upgrade -i \
  "weaviate" \
  weaviate/weaviate \
  --namespace "weaviate" \
  --values ./values.yaml
```

5. Verify the deployment

||| verify running pods
```
kubectl get pods -n weaviate
```
||| output
```
NAME         READY   STATUS    RESTARTS   AGE
weaviate-0   1/1     Running   0          11m
```
|||

---

||| verify pvc
```
kubectl get pvc -n weaviate
```
||| output
```
NAME                       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
weaviate-data-weaviate-0   Bound    pvc-dc2a6698-715a-40ec-a026-627a2a08c13d   10Gi       RWO            gp2            <unset>                 11m
```
|||

---

||| verify services
```
kubectl get svc -n weaviate
```
||| output
```
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP                                                                  PORT(S)           AGE
weaviate            LoadBalancer   10.100.88.131   a5dc7b9ffa1cf43419b81c984c7f4baa-1392225488.eu-central-1.elb.amazonaws.com   80:31705/TCP      12m
weaviate-grpc       LoadBalancer   10.100.18.216   a241351282df84f569af7f824a8a8c1d-524398294.eu-central-1.elb.amazonaws.com    50051:32235/TCP   12m
weaviate-headless   ClusterIP      None            <none> 
```
|||

Your Weaviate deployment is now accessible on the external IP address of `weaviate` service, e.g. `a5dc***8.eu-central-1.elb.amazonaws.com`

After correct deployment there are next steps that you might want to consider:

* To keep up with your needs, you might want to [Scale Weaviate](https://weaviate.io/developers/weaviate/concepts/cluster)
* When moving to production deployments you should consider adopting the Infrastructure as Code (IaC) methodology. It often helps with
disaster recovery.
  * Example would be Terraform
* Follow security best practices: Enable Api Keys, OIDC
  * Restrict external access, e.g. to your organization's VPN
* Deploy a monitoring solution stack, e.g. Prometheus, Loki, Promtail, Grafana
  * Another popular tool is Alertmanager. It can help identify issues soon with integration with communication services like Slack, Teams, etc.
* Keep your Weaviate deployment up to date
