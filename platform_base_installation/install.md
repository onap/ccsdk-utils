### Cloudify Manager/Consul Installation

### Setting up for installation
#### Installation machine
This installation process is a _bootstrap process_, not a conventional software installation.  In a conventional installation, the installer spins up a VM and then runs a software installation on it.  That is *_not_* how this installation process works.  This installation targets an OpenStack tenant that is essentially empty. The installation process will create the VMs that it needs in the OpenStack tenant.  The installation process is launched from a machine *_that is not in the OpenStack tenant where the controller is being installed_*.

The installation machine--which, again, *_must not be in the tenant where the controller is being installed_* must have:
  - CentOS 7 operating system.  (CentOS 6 can be made to work also.)
  - A `bash` interpreter
  - `wget`, `curl`, and `unzip`
  - A python _development_ environment, including `gcc` and the appropriate Python development package for the distro.
  - The python `pip` (version 9.01 or later) and `virtualenv` (version 15.0.2 or later) tools.
  - Connectivity to this source code repository.
  - Connectivity to the repository holding deployment artifacts.
  - Connectivity to the Internet.
  - And, obviously, connectivity to the OpenStack tenant where the controller is being installed.

#### Tenant setup
Certain things need to be set up in the tenant in order to allow the installation to proceed. An `inputs.yaml` containing information about the
tenant must be created for use in the installation step.
TODO: Provide more details here.

#### Installing Cloudify Manager and Consul
Once the tenant is set up and the `inputs.yaml` file is available, Cloudify Manager and the Consul cluster can be
installed.  The `installer` script in this repository automates the installation.

To run the script:
  - Create a new directory on the installation machine
  - Enter the new directory
  - Place a copy of `installer` in the directory
  - `chmod +x installer`
  - `./installer --inputs /path/to/inputs/file --ssh-key /path/to/ssh-key --location-id location-id`, where:
    - `/path/to/inputs/file` is the path to the file generated with `configure-dcae`
    - `/path/to/ssh-key` is the path to a local copy of the private key for the keypair set up with `configure-dcae`
    - `location-id` is  the location id (e.g., `solutioning-central`) that will be used to identify the location where this deployment is being performed.  `location-id` sets the Consul datacenter name; it also is used in the Cloudify Manager environment file to mark the section of the file where information about this location is stored.



