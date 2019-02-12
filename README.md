# rancher2-vagrant

A vagrant box which runs a Rancher 2.0 server and single node Kubernetes cluster for local development.

- Runs a local VirtualBox VM with hostname `rancher2` and IP address `192.168.88.100`. For convenience it's assumed `192.168.88.100 rancher2` has been added to the `hosts` file.
- Installs docker version 18.03.1-ce
- Starts a [Rancher 2.0](https://hub.docker.com/r/rancher/rancher/) server container and sets the server url to https://rancher2:8443/ where you can access the Rancher UI.
- Starts a [Rancher Agent](https://hub.docker.com/r/rancher/rancher-agent) container that will create a local Kubernetes cluster and link it to the Rancher Server. The API is accessible on https://rancher2:443/.
- Also contains  [Portainer](https://portainer.io/) container because I like to be able to see which containers Kubernetes is running. Accessible at http://rancher2:9000/.

First version was based on the Chef base box [bento/ubuntu-18.04](https://github.com/chef/bento/tree/master/ubuntu)
and included all the base images, but was 1.3G in size. Now using Alpine Linux for a smaller base (around 80 MB) and only pre-pulling
the Rancher 2.0 Server image which is around 160 MB compressed.

## Build

If you have [Packer](https://www.packer.io/) and [VirtualBox](https://www.virtualbox.org/) installed you can run `build.sh` to create a new box image.

Startup of images is done in the Vagrantfile inline provision script so that
fresh server tokens are generated at that time and not included in the box image.

## Run

1. Add to local hosts file for convenient access
   ```
   192.168.88.100 rancher2
   ```
2. Install the Vagrant Alpine plugin `vagrant plugin install vagrant-alpine`
3. Use the supplied vagrant file and run `vagrant up`
4. Go to Rancher 2.0 and set the admin password: https://rancher2:8443/
5. Run `vagrant ssh` to log into the VM, then run `kubectl cluster-info`.
6. Once Portainer has been provisioned you can set the admin password: http://rancher2:9000/#/init/admin. Choose "Manage the local Docker environment" and connect.
   Sometimes I have to log out and in again after setting up the password.
7. Go to "Clusters" in the Rancher UI and see the state of the "local-cluster" which will
   probably take a while to download all the images and start the cluster.

## Useful references

- Alpine sections based on Matt Maier's [vagrant-alpine plugin](https://github.com/maier/vagrant-alpine), [alpine-vagrant 3.8 box](https://github.com/rgl/alpine-vagrant) and [packer-templates](https://github.com/maier/packer-templates/)
- https://ketzacoatl.github.io/posts/2017-06-01-use-existing-vagrant-box-in-a-packer-build.html describes how to build using another vagrant box as base, this build originally used https://vagrantcloud.com/bento/boxes/ubuntu-18.04/versions/201807.12.0/providers/virtualbox.box
- see https://gist.github.com/superseb/29af10c2de2a5e75ef816292ef3ae426 for example of Rancher 2 REST API calls to create and add a new cluster
- see https://gist.github.com/superseb/cad9b87c844f166b9c9bf97f5dea1609 for example of Rancher 2 REST API calls to create kubeconfig
