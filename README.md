# Terraform AWS EKS

Terraform scripts to deploy an EKS managed Kubernetes cluster into AWS. These scripts are based entirely on Hashicorps "Getting Started with AWS EKS"
article found [here](https://www.terraform.io/docs/providers/aws/guides/eks-getting-started.html).

## Requirements

- AWS account with sufficient privileges to create AutoScaling groups, EC2 instances, EKS, and IAM resources.
- AWS account credentials setup as outline [here](https://www.terraform.io/docs/providers/aws/index.html#authentication).
- Terraform in a semi-modern state.

## Usage

First, replace `WORKSPACE_IP` in `eks_master.tf` with the IP address of your local workspace.

Prior to running a Terraform apply, you will likely need the AWS provider plugin. To obtain this,
run the command

```
terraform init
```

This will create a `.terraform` directory containing the plugin files. After this, run the command

```
terraform apply
```

to begin the deployment. Note that the deployment can take quite some time, as the `aws_eks_cluster` resource
takes quite a while to provision (usually around the 10 minute mark).

Once completed, take the resulting Kube Config file and copy it into your directory containing Kubernetes
configuration files. This will likely reside in `~/.kube/`. You may also want to add this to your `KUBECONFIG`
environment variable using:

```
export KUBECONFIG=$KUBECONFIG:$HOME/.kube/(FILE_NAME_HERE)
```

Run the command

```
kubectl config use-context aws
```

to switch to the newly created cluster context.

Run the command 

```
terraform output config_map_aws_auth
```

and save the result into a configuration file `config_map_aws_auth.yaml`, then run the command

```
kubectl apply -f config_map_aws_auth.yaml
``` 

Finally, run the command

```
kubectl get nodes --watch
```

to ensure worker nodes join the cluster.


