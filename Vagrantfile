Vagrant.configure("2") do |config|
  
    config.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end

    #so that we could use the private_key files, (not too much permission) and execute bash files too let's mount files with 700 permission
    config.vm.synced_folder "./", "/vagrant",
    owner: "vagrant",
    mount_options: ["dmode=775,fmode=700"]

    config.vm.define "ac", primary: true do |box|
      box.vm.network "private_network", ip: "192.168.10.10"
      box.vm.box = "ubuntu/impish64"
      box.vm.hostname = "ac"
      box.vm.provider "virtualbox" do |v|
        v.name = "ubuntu-ansible-controller"
        v.memory = 2000
        v.cpus = 2
      end 
      box.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y ansible sshpass gnupg gnupg2 python3-pip
        python3 -m pip install "pywinrm>=0.3.0"
        python3 -m pip install pypsexec
        sudo timedatectl set-timezone America/Toronto
        sudo mkdir /etc/ansible/roles -p
        sudo chmod o+w /etc/ansible/roles
        ansible-galaxy collection install chocolatey.chocolatey
        ansible-galaxy collection install community.windows
      SHELL
    end
  end