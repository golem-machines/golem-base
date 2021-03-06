# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # detect windows
  require 'rbconfig'
  is_windows = (RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/)

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ubuntu/trusty64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  # config.vm.box_url = "http://files.vagrantup.com/trusty64.box"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network :forwarded_port, guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network :private_network, ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # load dynamic configuration from vm-config file
  if (File.exists? './vm-config.json')
    require 'json'
    cfg = JSON.parse(IO.read('./vm-config.json'))

    if cfg.key?('folders')
      cfg['folders'].each do | host_dir, guest_dir |
        if is_windows and hostDir.match(/\/[A-Za-z]\//)
          # convert to windows path
          host_dir = host_dir[1..-1].sub("/", ":\\").gsub("/", "\\")
        end
        config.vm.synced_folder host_dir, guest_dir
      end
    end

    if cfg.key?('portForwarding')
      cfg['portForwarding'].each do | guest_port, host_port |
        config.vm.network :forwarded_port, guest: guest_port, host: host_port
      end
    end

  end

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  config.vm.provider :virtualbox do |vb|
#   # Don't boot with headless mode
#   vb.gui = true

    # Use VBoxManage to customize the VM. For example to change memory:
    vb.memory = 2048

    host_cpus = 2
    begin
      if is_windows
        require 'win32ole'
        host_cpus = WIN32OLE.connect("winmgmts://").ExecQuery("select * from Win32_ComputerSystem").NumberOfProcessors
      else
        if File.exist? '/proc/cpuinfo'
          host_cpus = File.read('/proc/cpuinfo').scan(/^processor\s*:/).size
        else
          host_cpus = `sysctl -n hw.logicalcpu`.chomp.to_i
        end
      end
    rescue
      host_cpus = 2
    end
    vb.cpus = host_cpus
  end

  # provision with shell script
  config.vm.provision :shell, :path => "scripts/provision.sh"

end
