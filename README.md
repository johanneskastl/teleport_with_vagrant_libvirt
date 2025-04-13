# teleport_with_vagrant_libvirt

This Vagrant setup creates a teleport-server VM and two VMs as "nodes".

Default OS is openSUSE Leap 15.6, but that can be changed in the Vagrantfile.
Please beware, this might break the ansible provisioning which was only tested
with openSUSE Leap 15.6.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Party!

To create your first user, you need to log in on the teleport-server node via
`vagrant ssh teleport-server` and create a user:

```
tctl users add first_user --roles auditor,editor,access --logins root,vagrant
```

After that you can open the teleport-server's IP address in your browser, ignore
the warning due to the self-signed certificate and log in as that user.
You should see three "servers" in the "Server" tab.
Tadaa!

## Creating additional agent nodes

You can modify the Vagrantfile to create additional agent nodes by tweaking two
lines.

1. Setting the number of agents (in this example to `2`):

```
  ###################################################################################
  # define number of agents
  W = 2
```

2. Adding the additional agent nodes to the `ansible_groups` line:

```
      ansible.groups = {
        "teleport_servers"  => [ "teleport-server" ],
        "teleport_nodes"   => [ "teleportnode1", "teleportnode2" ]
      }
```

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the files for the demo CA in `ansible/Teleport_demo_CA/`.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via git@johannes-kastl.de
