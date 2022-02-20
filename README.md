# teleport_with_vagrant_libvirt

This Vagrant setup creates a teleport-server VM and two VMs as "nodes".

Default OS is openSUSE Leap 15.3, but that can be changed in the Vagrantfile. Please beware, this might break the ansible provisioning which was only tested with openSUSE Leap 15.3.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
2. Fetch the box, per default this is `opensuse/Leap-15.3.x86_64`, using `vagrant box add opensuse/Leap-15.3.x86_64`.
3. Make sure the git submodules are fully working by issuing `git submodule init && git submodule update`
4. Create the SSL CA on the ansible control node (where you run vagrant): `ansible-playbook ansible/playbook-localhost-selfsigned_demo_ca.yml`
5. Run `vagrant up`
6. Party!

To create your first user, you need to log in on the teleport-server node via `vagrant ssh teleport-server` and create a user:
```
tctl users add first_user --roles auditor,editor,access --logins root,vagrant
```

After that you can open the teleport-server's IP address in your browser, ignore the warning due to the self-signed certificate and log in as that user.
You should see three "servers" in the "Server" tab.
Tadaa!

## Disabling the Ansible provisioning

In case you do not want Ansible to install teleport (because you want to install it yourself), just comment out the following lines in the `Vagrantfile`:
```
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.limit = "all"
      ansible.groups = {
        "teleport_servers"  => [ "teleport-server" ],
        "teleport_nodes"   => [ "teleportnode1", "teleportnode2" ]
      }
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision
```

## Cleaning up

When tearing down the machines, the files for the self-signed SSL CA are not being deleted automatically. You can remove them, if you do no longer need them, by using `rm -r ansible/Teleport_demo_CA/`.

## Creating additional agent nodes

You can modify the Vagrantfile to create additional agent nodes by tweaking two lines.

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
