### Consul configuration for ECOMP and DCAE controllers

#### Version and source
This installation uses Consul 0.8.3.

Consul is an open source project, and it can be built from source.  For this project, however, we are using a binary distribution provided by Hashicorp (the company responsible for Consul development), pulled from https://releases.hashicorp.com/consul/0.8.3/consul_0.8.3_linux_amd64.zip.  The zip file contains a single file, the consul
binary executable.

#### How the installation works
The installation is driven by a Cloudify blueprint ([`consul_cluster.yaml`](./consul_cluster.yaml)) with a set of inputs describing the OpenStack environment (typically generated by running the `configure-dcae` command as described in
the [installation instructions](./install.md)).

The blueprint creates three virtual machines, each running Ubuntu 16.04.   The blueprint includes an initial shell script that runs when the VM is first booted (the so-called `cloud-init` script).  This shell script:
- creates the `/opt/consul/bin`, `/opt/consul/data`, and `/opt/consul/config`, and `/opt/consul/data` directories
- installs the consul binary in `/opt/consul/bin/consul`.
- creates a Consul configuration file and installs it in `/opt/consul/config/consul.json`.
- creates a `systemd` unit file that contains instructions for starting Consul when the system boots and stores it in
`/lib/systemd/system/consul.service`.  The Consul software is started in `server` mode.
- enables the Consul service so that `systemd` will start it on boot.
- explicitly starts the Consul service.

The blueprint launches two VMs in parallel.  It does not attempt to launch the third VM until the first two have started and their private IP addresses are known.

The initialization script for the third VM has one additional step, at the very end.  The script issues a `consul join` command targeting the first two machines.  This creates the cluster.

After Cloudify finishes with the installation, the [`installer` script](./installer) waits for the Consul API to become available on the first Consul server, and then attempts to register Cloudify Manager as an [external service](https://www.consul.io/docs/guides/external.html) in Consul.  It also puts the address of the first server into the `/opt/env.ini` file on the Cloudify Manager host.  (It puts in one address to maintain consistency with the earlier installation process that installed a single Consul server in a Docker container.  Certain plugins rely on this entry in `/opt/env.ini`, and they expect a single address.  This will eventually change.)

#### Managing Consul
We don't anticipate a need for hands-on management of Consul.  When a Consul VM is rebooted, Consul will start automatically and will rejoin the Consul cluster.

If there's a need to look more closely at what's going on with a Consul VM, logging in and becoming root provides access to the `systemd` tools.  In particular:

- `systemctl stop consul` will stop the consul service.
- `systemctl start consul` with start the consul service.
- `journalctl -u consul` will show the log for the consul service.
- `journalctl -u consul -f` will "tail" the log for the consul service.

The crucial files for Consul are located in:
- `/opt/consul/config/config.json` - the Consul configuration file. See the [Consul documentation of configuration options](https://www.consul.io/docs/agent/options.html).
- `lib/systemd/system/consul.service` - the `systemd` unit file for the Consul service.
- `/opt/consul/data` - directory where Consul stores its persistent data.

Note that the Consul instances communicate with each other over the OpenStack private network--that is, they use private addresses, rather than the floating IP addresses, to contact each other.
