Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of nodes
  W = 2

  # provision W VMs as nodes
  (1..W).each do |i|

    # name the VMs
    config.vm.define "teleport-node#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.3.x86_64"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = true
        lv.memory = 1024
        lv.cpus = 2
      end

      # set the hostname
      node.vm.hostname = "teleport-node#{i}"

    end # config.vm.define nodes

  end # each-loop nodes

  ###############################################

  # name the VMs
  config.vm.define "teleport-server" do |node|

    # which image to use
    node.vm.box = "opensuse/Leap-15.3.x86_64"

    # sizing of the VMs
    node.vm.provider "libvirt" do |lv|
      lv.random_hostname = true
      lv.memory = 2048
      lv.cpus = 2
    end

    # set the hostname
    node.vm.hostname = "teleport-server"

    #################################################
    # only target one node to not run ansible twice
    # but do not limit this to this node (set ansible.limit to all)
    node.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.limit = "all"
      ansible.groups = {
        "teleport_servers"  => [ "teleport-server" ],
        "teleport_nodes"   => [ "teleport-node1", "teleport-node2" ]
      }
      ansible.playbook = "ansible/playbook-vagrant.yml"
    end # node.vm.provision

  end # config.vm.define servers

end # Vagrant.configure
