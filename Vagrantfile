# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  # Need to install the disksize plugin:
  # vagrant plugin install vagrant-disksize
  config.disksize.size = '50GB'

  config.ssh.forward_x11 = true

  config.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "4096"
     vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
   end

  config.vm.provider "xenserver" do |xs,override|
    override.vm.box = "jonludlam/ubuntu-16.04"
    override.vm.network "public_network", bridge: "xenbr0"
  end

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

# Get the VM up-to-date
    sudo apt-get update

# Desktop
    sudo apt-get install -y xauth xorg openbox lightdm plymouth
    sudo apt-get install -y ubuntu-desktop

# Install vagrant
    wget https://releases.hashicorp.com/vagrant/1.9.1/vagrant_1.9.1_x86_64.deb --quiet
    sudo apt-get install -y ./vagrant_1.9.1_x86_64.deb
    sudo apt-get install -y ansible
    vagrant plugin install vagrant-xenserver

# Install go tools
    sudo apt-get install git
    wget https://storage.googleapis.com/golang/go1.8.1.linux-amd64.tar.gz --quiet
    sudo tar -C /usr/local -xzf go1.8.1.linux-amd64.tar.gz
    export PATH=$PATH:/usr/local/go/bin
    mkdir ~/go
    export PATH=$PATH:~/go/bin
    go get github.com/mitchellh/gox
    go get github.com/hashicorp/packer
    go get github.com/mitchellh/go-vnc

# Packer-builder-xenserver
    mkdir -p ~/go/src/github.com/xenserver/
    cd ~/go/src/github.com/xenserver/
    git clone https://github.com/xenserver/packer-builder-xenserver.git
    cd packer-builder-xenserver
    ./build.sh

# Install opam
    sudo apt-get install -y m4 libxen-dev
    # Install opam 2:
    sudo curl -sL https://github.com/ocaml/opam/releases/download/2.0.1/opam-2.0.1-x86_64-linux --output /usr/local/bin/opam
    sudo chmod +x /usr/local/bin/opam
    opam init --disable-sandboxing --auto-setup --compiler=4.06.1 --yes
    eval `opam env`
    opam remote add xs-opam git://github.com/xapi-project/xs-opam
    opam install -y opam-depext

# Install dependencies of xapi and verify it can be built
    sudo apt-get install -y git
    git clone git://github.com/xapi-project/xen-api
    cd xen-api
    opam depext -y xapi
    opam install -y --deps-only .
    ./configure; make; make test > test.log; cd ..

# Install OCaml development tools
    opam install -y merlin ocp-indent ocp-index ocp-browser utop
    # ocp-index completion
    sudo curl https://raw.githubusercontent.com/OCamlPro/ocp-index/master/tools/oct.sh -o /etc/bash_completion.d/oct

# Install Sublime Text
    sudo apt-add-repository -y "ppa:webupd8team/sublime-text-3"
    sudo apt-get update
    sudo apt-get install -y sublime-text-installer

# Install Visual Studio Code
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
    sudo apt-get update
    sudo apt-get install -y code
    code --install-extension hackwaly.ocaml

# Install Atom
    sudo apt-add-repository -y "ppa:webupd8team/atom"
    sudo apt-get update
    sudo apt-get install -y atom
    apm install nuclide ocaml-merlin language-ocaml

# Install vim
    sudo apt-get update
    sudo apt-get install -y vim

# Install emacs
    sudo apt-get update
    sudo apt-get install -y emacs

# Automatically configure installed editor/s to use installed OCaml tools
    opam install user-setup
    opam user-setup install

# Fix gnome-terminal
    sudo localectl set-locale LANG="en_GB.utf8"

# Install Docker - required by planex-buildenv
    sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
    sudo apt-add-repository 'deb https://apt.dockerproject.org/repo ubuntu-xenial main'
    sudo apt-get update
    sudo apt-get install -y docker-engine
    sudo usermod -aG docker vagrant


# Reboot required to ensure locale and profile changes are picked up
# We actually shut down because a `vagrant up` does some setting up.
    sudo poweroff

  SHELL
end
