# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "boxcutter/ubuntu1604-desktop"

  config.ssh.forward_x11 = true

  config.vm.network "public_network"

  config.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "4096"
   end
  
  config.vm.provision "shell", privileged: false, inline: <<-SHELL

# Get the VM up-to-date
    sudo apt-get update
    sudo apt-get upgrade

# Install opam and things for compiling xapi
    sudo apt-get install -y opam m4 libxen-dev git
    opam init -a --compiler=4.02.3 -y
    opam remote add xs-opam git://github.com/xapi-project/xs-opam
    opam install -y merlin ocp-indent utop lwt_react depext camlp4
    sudo opam depext -y xapi
    opam pin add xenctrl 0.9.32
    opam install ezxenstore
    opam install --deps-only xapi

# Install editors
    sudo apt-get install xubuntu-desktop gksu leafpad synaptic
    sudo add-apt-repository -y "ppa:webupd8team/sublime-text-3"
    sudo apt-add-repository -y "ppa:webupd8team/atom"
    sudo apt-get update
    sudo apt-get install -y atom emacs vim sublime-text-installer
    curl -L https://go.microsoft.com/fwlink/\?LinkID=760868 -o /tmp/code.deb
    sudo apt-get install -y /tmp/code.deb
    apm install nuclide ocaml-merlin language-ocaml
    code --install-extension hackwaly.ocaml
    opam install user-setup
    opam user-setup install

# Fix atom and VS code
    sudo sed -i s^/opt/atom/atom^/opt/atom/atom\\ --disable-gpu^g /usr/share/applications/atom.desktop
    sudo sed -i s^/usr/share/code/code^/usr/share/code/code\\ --disable-gpu^g /usr/share/applications/code.desktop

# Fix gnome-terminal
    sudo localectl set-locale LANG="en_GB.utf8"

    sudo reboot
  SHELL
end
