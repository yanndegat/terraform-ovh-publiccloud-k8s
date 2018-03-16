# Simple k8s cluster from a prebuilt glance image


- [Simple k8s cluster](#simple-k8s-cluster)
    - [Configuration](#configuration)
    - [Run it](#run-it)
    
## Install terraform

You can read documentation on how to install terraform [here](https://www.terraform.io/intro/getting-started/install.html)

## Configuration
1. Copy variable file

There is an example of var file `terraform.tfvars.sample`.

Copy it under the name `terraform.tfvars` (this allow terraform to autoload those variables)

2. Create a public cloud project on OVH

Follow the [official documentation](https://docs.ovh.com/gb/en/public-cloud/getting_started_with_public_cloud_logging_in_and_creating_a_project/).

Create an Openstack user using the "Openstack" part of your cloud project. 

Source your openstack configuration file. Generate it for your user using the OVH manager (Use the user contextual menu). 

```bash
$ source openrc.sh
Please enter your OpenStack Password: 

```

3. Create or reuse a ssh key pair. Careful this keypair should not be using passphrase!

```bash
# Generate a new keypair without passphrase
$ ssh-keygen -f terraform_ssh_key -q -N "" -t rsa -b 4096
```

If you generate a new keypair, put its path in `terraform.tfvars` under variable `public_sshkey` and add it to your ssh-agent:
```bash
$ ssh-add terraform_ssh_key
```

## Build the image

Please refer to the [provided example](../k8s-glance-image/README.md)

## Run it

```bash
$ terraform init
Initializing modules...
- module.network
- module.kube
[...]
Terraform has been successfully initialized!

$ terraform apply
[...]
helper = Your kubernetes cluster is up.

You can connect to one of the instances:

    $ ssh core@<ip>

Check your etcd cluster:

    $ /opt/etcd/bin/etcdctl --ca-file /opt/etcd/certs/ca.pem --cert-file /opt/etcd/certs/cert.pem --key-file /opt/etcd/certs/cert-key.pem --endpoints https://54.36.112.50:2379 member list

Run a pod:

    $ ...

Enjoy!
```

This should give you an infra with:

* 3 kubernetes masters in a public network with:
  * Canal (Flannel + Calico) CNI
  * Untainted nodes (pods can run on masters)
  * kube-proxy for services