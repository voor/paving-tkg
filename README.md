# Paving into VMware Tanzu Kubernetes Grid

[VMware Tanzu Kubernetes](https://tanzu.vmware.com/kubernetes-grid) has reached general availability. It provides an enterprise-ready way to streamline operations for Kubernetes, and some other fun stuff.  As a fun thought experiment, I incorporated the TKG cluster into an existing Pivotal Paving motion.

## Run Paving

As a prequisite, run [Pivotal Paving](https://github.com/pivotal/paving) on your AWS environment to create the necessary VPC and subnets for TKG to leverage.

## Generate Cluster Manifest

Once you've created your paved AWS environment, we'll need to change some stuff in TKG.

Run the following:

```shell
# Where you checked out paving
cd ~/workspace/paving/aws
terraform output stable_config > ~/workspace/paving-tkg/stable_config.json
# Where you checked out this repo
cd ~/workspace/paving-tkg/
# This is a default location
ytt --ignore-unknown-comments --data-value-file stable_config=stable_config.json \
    -f ~/.tkg/providers/infrastructure-aws/v0.5.2/cluster-template-dev.yaml \
    -f overlay-vpc.yaml \
        -f values.yaml > ~/.tkg/providers/infrastructure-aws/v0.5.2/cluster-template-paving.yaml

# Now just init tkg with the paving plan.
tkg init --infrastructure aws:v0.5.2 -p paving --name capa
```

That's it.  Enjoy your TKG in the same VPC as Tanzu Application Service!