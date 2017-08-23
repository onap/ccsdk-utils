### ONAP Controller Core Components Installation
The ONAP Operations Manager and the next-generation ONAP DCAE system use Cloudify Manager, a third party open source product,
as the core orchestration engine.  They use Consul, another third party open source product, as
a key-value store, a service discovery system, and a health monitoring tool.

This repository contains scripts and artifacts that are used to create
a VM in an OpenStack environment and then install the Cloudify Manager software on it.
Once the Cloudify Manager software is installed, the script uses Cloudify Manager to deploy
a 3-VM  cluster running Consul in a high-availability mode.  When the deployment completes, the script registers
Cloudify Manager as a service in Consul and puts Consul's address into a configuration file
on the Cloudify Manager VM.

Eventually we expect that the ECOMP controller will launch the next-generation DCAE controller using a Cloudify blueprint that's executed by the ECOMP controller's Cloudify Manager system.  Until that system is ready, the script and artifacts
here can be used to deploy a Cloudify Manager and a Consul cluster for the DCAE controller.

Documentation in this repo:
- The installation instructions are in the [`install.md` file](./install.md).
- A description of how the installation process sets up Consul is in the [`consul.md` file](./consul.md).

External documentation links:

- [Information about Cloudify](http://getcloudify.org)
- [Information about Consul](http://consul.io)


#### Limitations
- The installation process does _not_ set up TLS certificates for any of the host VMs and therefore does not configure Cloudify Manager and Consul to use HTTPS.
- The installation process does _not_ set up authentication, authorization, and access control for Cloudify Manager and Consul.
- The installation process sets up one non-root user on each VM.  The user is configured for access using the keypair provided at installation time.
- The Cloudify Manager is _not_  set up for highly availability.
- While Consul _is_ set up in a high-availability configuration, clients need to know the addresses of all three Consul VMs.  In the event that a client cannot reach one of the VMs, the client must try another address.
- The installation process installs the Web-based user interfaces for both Cloudify Manager and Consul.  This may not desirable for production environments.