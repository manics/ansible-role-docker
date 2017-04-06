Docker
======

Setup Docker, provides options for using an advanced storage or networking configuration.
Installs the latest official upstream Docker (**not** the distribution version).


Role Variables
--------------

Defaults: `defaults/main.yml`

- `docker_use_custom_storage`: If `True` use a custom storage configuration, default `False`
- `docker_use_custom_network`: If `True` use a custom network configuration, default `False`

Custom storage: If `docker_use_custom_storage` is `True` thin-pool logical volumes will be created for Docker, and a separate logical volume will be created for the Docker volume (`/var/lib/docker`).

- `docker_basefs`: Filesystem to use for the Docker containers (default xfs)
- `docker_lvfilesystem`: Filesystem for the Docker volume (default xfs)

The following variables must be defined:

- `docker_vgname`: LVM volume group for the logical volumes
- `docker_poolsize`: Size of the Docker thin-pool partition
- `docker_metadatasize`: Size of the Docker thin-pool metadata partition (try 1% of the poolsize)
- `docker_volumesize`: Size of the Docker volume

Custom networking: If `docker_use_custom_network` is `True` a custom network bridge will be used, this must be created outside of this role.
The following variables must be defined:

- `docker_bridge_name`: The name of a custom network bridge for docker
- `docker_bridge_ips`: The custom IP range that docker should use for allocating IPs

Optional variables:

- `docker_lvopts`: Additional arguments to be used when creating logical volumes
- `docker_use_ipv4_nic_mtu`: Force Docker to use the MTU set by the main IPV4 interface. This may be necessary on virtualised hosts, see comment in `defaults/main.yml`.
- `docker_groupmembers`: A list of users who will be added to the `docker` system group, allows docker to be run without sudo


Dependencies
------------

Depends on lvm-partition.


Example Playbook
----------------

Simple example (uses default storage overlay driver):

    - hosts: localhost
      roles:
        - role: openmicroscopy.docker

Advanced example using custom storage (the LVM volume group `VolGroup00` must already exist):

    - hosts: localhost
      roles:
        - role: openmicroscopy.docker
          docker_use_ipv4_nic_mtu: True
          docker_use_custom_storage: True
          docker_vgname: VolGroup00
          docker_poolsize: 10g
          docker_metadatasize: 100m
          docker_volumesize: 5g
          docker_groupmembers: [centos]


Author Information
------------------

ome-devel@lists.openmicroscopy.org.uk
