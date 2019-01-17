
# Toolstack Development Environment

Here we describe a supported environment setup for developing and building
Toolstack components. We currently do all testing of this development
environment using a Ubuntu 16.04 base image; it may be possible to get
everything working on other distributions but we cannot guarantee it.

The Toolstack uses many upstream components, which can sometimes mean that
a new release of an upstream component may break our environment/build
(due to our loosely-defined dependencies). In this development
environment, the dependencies are as loosely-defined as possible because
it is best to see which latest changes break our builds and which do not.

Once it is recognized that a wrong dependency is being used
to build a component, we can pin a specific version of that
dependency which is compatible with the component we are trying to
build. This would serve as a temporary solution for getting the
correct dependency; this dependency would ultimately be fixed in
[xs-opam](https://github.com/xapi-project/xs-opam), removing the need
to pin it in our environment.

## Vagrant/VirtualBox VM

The `Vagrantfile` is used by Vagrant to setup and run a VirtualBox
VM with all the necessary dependencies/tools for developing Toolstack
components and running their unit tests.

This VM serves as a way to quickly test that the development environment
works and also enables us to quickly recognize when something is broken
(such as an upstream change breaking our build).

You can create and run the VM with any platform supported by
[VirtualBox](https://www.virtualbox.org/wiki/Downloads) and
[Vagrant](https://www.vagrantup.com/docs/installation/).

The `Vagrantfile` uses the `vagrant-disksize` plugin to increase the VM's disk.
Install it by running `vagrant plugin install vagrant-disksize`.

The VM can be created/run in VirtualBox using the `vagrant up --provider
virtualbox` command. There may be an issue where this may fail the first time
after a few seconds; run `vagrant provision` to restart the provisioning step
of the setup and it should continue as normal.

## Setting up locally

The recommended way to develop and build is to set everything up locally
on your own Ubuntu 16.04 system; this way you are able to use your own
environment with all your favourite tools.

Read through the `Vagrantfile` to understand the tools used to build
and develop the Toolstack components. Then, execute all the steps which
apply to your setup; for example, you may choose to only install one/some
of the supported text editors, whereas the `Vagrantfile` sets up all of
the available ones.

## Building different components

In the `Vagrantfile`, `xapi` is built in order to verify that the
environment has been set up correctly. In order to build other components,
their dependencies must also be installed.

Using `xapi-xenopsd` as an example, its external dependencies must first be
installed using `depext`. This will install any operating system packages
required by OCaml to compile `xapi-xenopsd`:

```
opam depext -y xapi-xenopsd
```

Some specific version of one or more packages may need to be pinned using
`opam pin add <package> <version>` in order to compile `xapi-xenopsd`. This
is needed if the component is temporarily broken due to an upstream
dependency making breaking changes which we have not yet updated in
[xs-opam](https://github.com/xapi-project/xs-opam) (or *cannot* yet
change the dependency). For `xapi-xenopsd` there is no additional pinning
currently required.

Then, the opam packages which `xapi-xenopsd` depends on must be installed:

```
opam install --deps-only xapi-xenopsd
```

Finally, you can build `xapi-xenopsd` and run its tests:

```
git clone https://github.com/xapi-project/xenopsd
cd xenopsd
./configure
make
make test
```
