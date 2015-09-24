# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Global Variables
GUEST_PROJ_DIR = "C:\\vagrant\\"
GUEST_MODULE_DIR = GUEST_PROJ_DIR + "DSC\\Modules\\"
GUEST_MOF_DIR = GUEST_PROJ_DIR + "DSC\\MOF\\"
GUEST_HOSTNAME = "SitecoreServerTest"

# Script to Load DSC Module Dependencies
#  - Installs Third-Party modules via PowerShellGet
#  - Installs the Project's Custom DSC Modules
$dscModDepScript = <<SCRIPT
  Write-Host "Installing Third-Party DSC Modules"
  Install-Module -Name xWebAdministration -Version 1.3.2.2
  if (Test-Path($Args[0])) {
    Write-Host "Installing Project DSC Modules"
    Copy-Item ($Args[0]) -Destination (\$env:ProgramFiles + "\\WindowsPowerShell\\Modules\\") -Recurse -Container -Force
  }
  Get-DscResource
SCRIPT


# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ws2012_r2_wmf5"
  config.vm.guest = "windows"
  config.vm.communicator = "winrm"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false
  
  config.vm.hostname = GUEST_HOSTNAME
  
  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine.
  config.vm.network :forwarded_port, host: 33389, guest: 3389
  config.vm.network :forwarded_port, host: 8080, guest: 80
  config.vm.network :forwarded_port, host: 4443, guest: 443

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  #config.vm.synced_folder "Data/", GUEST_PROJ_DIR + "/vagrant_data"

  # VirtualBox specific configuration so you can fine-tune various
  config.vm.provider :virtualbox do |vb|
    # Display the VirtualBox GUI when booting the machine
    #vb.gui = true
    vb.cpus = 2
    vb.memory = 4096
  end
  
  # Install DSC Module Dependencies
  config.vm.provision :shell do |s|
    s.inline = $dscModDepScript
    s.args = [GUEST_MODULE_DIR + "*"]
  end
  
   # Initialize DSC Configuration and Generate MOF file
  config.vm.provision :shell do |s|
    s.path = "DSC/Config/SitecoreDscConfig.ps1"
    s.args = [GUEST_HOSTNAME, GUEST_MOF_DIR]
  end

  # Apply DSC Configuration
  config.vm.provision :shell do |s|
    s.inline = "Start-DSCConfiguration -Path $Args[0] -Force -Wait -Verbose -Debug"
    s.args = [GUEST_MOF_DIR + "SetupSitecoreDevelopmentEnv\\"]
  end
    
  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
