# tf12
## EKS cluster loosely based on:
https://github.com/cloudposse/terraform-aws-eks-cluster/blob/master/examples/complete/main.tf

## AWS EKS cluster from scratch
1. create kms key is us-east-2: brahma0
  + key arn: `arn:aws:kms:us-east-2:240195868935:key/9bfd5f30-3ff4-40d1-bbc5-3d79c1251d52`
2. copy cloudposse files into this project and add to .gitignore
  + `cp -R /Users/robert/documents/code/terraform-aws-eks-cluster .`
  + add to gitignore:  cloudpossee/*
3. use the kms key created from the console: `export TF_VAR_cluster_encryption_config_kms_key_id=arn:aws:kms:us-east-2:240195868935:key/9bfd5f30-3ff4-40d1-bbc5-3d79c1251d52`
4. edit *main.tf* and ping each module to the version you want to use
5. examine *variables.tf*  to learn the variable structure for the module; edit  *terraform.tfvars.tf* to configure the variables
  + `variable "enabled_cluster_log_types"` **This will install several logs that will quickly accumlate and incur costs:**
  + [`api`, `audit`, `authenticator`, `controllerManager`, `scheduler`]
  + enable these at first for cluster validation/learning purposes, but quickly disable to avoid costs
6. **deployment**
  + `terraform init`
  + `terraform plan`
  + `terraform apply`

## initial cluster validation
- record the intial `terraform apply` output:

```
eks_cluster_arn = "arn:aws:eks:us-east-2:240195868935:cluster/ruben-dev-brahma0-cluster"
eks_cluster_endpoint = "https://B06C54FC74FDCC1B1116F85C0BA4B8EF.gr7.us-east-2.eks.amazonaws.com"
eks_cluster_id = "ruben-dev-brahma0-cluster"
eks_cluster_identity_oidc_issuer = "https://oidc.eks.us-east-2.amazonaws.com/id/B06C54FC74FDCC1B1116F85C0BA4B8EF"
eks_cluster_managed_security_group_id = "sg-069c25860a3d17a7f"
eks_cluster_version = "1.21"
eks_node_group_arn = "arn:aws:eks:us-east-2:240195868935:nodegroup/ruben-dev-brahma0-cluster/ruben-dev-brahma0-workers/9ec07415-030d-8862-d22c-fc6264eac7a3"
eks_node_group_id = "ruben-dev-brahma0-cluster:ruben-dev-brahma0-workers"
eks_node_group_resources = tolist([
  tolist([
    {
      "autoscaling_groups" = tolist([
        {
          "name" = "eks-ruben-dev-brahma0-workers-9ec07415-030d-8862-d22c-fc6264eac7a3"
        },
      ])
      "remote_access_security_group_id" = ""
    },
  ]),
])
eks_node_group_role_arn = "arn:aws:iam::240195868935:role/ruben-dev-brahma0-workers"
eks_node_group_role_name = "ruben-dev-brahma0-workers"
eks_node_group_status = "ACTIVE"
private_subnet_cidrs = [
  "172.16.0.0/19",
  "172.16.32.0/19",
]
public_subnet_cidrs = [
  "172.16.96.0/19",
  "172.16.128.0/19",
]
vpc_cidr = "172.16.0.0/16"
```
- note the cluste endpoint: this shoduld be an Internet-reachable URL
  + https://b06c54fc74fdcc1b1116f85c0ba4b8ef.gr7.us-east-2.eks.amazonaws.com/

- **setup kubectl**
  + note the `eks_cluster_id` from the terraform output: use this as the "name" parameter in the kubectl command
  + `aws eks update-kubeconfig --region us-east-2 --name ruben-dev-brahma0-cluster`
    - should see: Added new context arn:aws:eks:us-east-2:240195868935:cluster/ruben-dev-brahma0-cluster to /Users/robert/.kube/config

- kubectl checkout commands
  + `kubectl config get-contexts`
  + `kubectl version -o json`
  + `kubectl get svc`
- iam validation: `aws cloudtrail lookup-events --region us-east-2 --lookup-attributes AttributeKey=EventName,AttributeValue=CreateCluster`
  + this shoudl indicate the user who created the cluster and the user who will be deploying the kubernetes manifiests for app deployments 
- validate ECR repositories:  `aws ecr describe-repositories`
## Links
https://github.com/cloudposse/terraform-aws-eks-cluster/blob/master/examples/complete/main.tf
https://faun.pub/aws-eks-and-pods-sizing-per-node-considerations-964b08dcfad3
https://stackoverflow.com/questions/57970896/pod-limit-on-node-aws-eks
https://github.com/awslabs/amazon-eks-ami/blob/master/files/eni-max-pods.txt