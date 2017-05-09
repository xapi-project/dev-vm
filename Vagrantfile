# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "boxcutter/ubuntu1604-desktop"

  config.ssh.forward_x11 = true

  config.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "4096"
   end

  config.vm.provider "xenserver" do |xs|
    xs.use_himn = true
  end

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

# Get the VM up-to-date
    sudo apt-get update

# Install opam and dependencies for compiling xapi
    sudo apt-get install -y opam m4 libxen-dev
    opam init -a --compiler=4.02.3 -y
    eval `opam config env`
    opam remote add xs-opam git://github.com/xapi-project/xs-opam
    opam install -y lwt_react depext camlp4
    sudo opam depext -y xapi
    # due to constraints missing from old libraries, pinning the lwt package is necessary for certain configurations
    opam pin add lwt 2.7.1
    opam install --deps-only xapi

# Verify xapi can be built
    sudo apt-get install -y git
    git clone git://github.com/xapi-project/xen-api
    cd xen-api; ./configure; make; make test > test.log; cd ..

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
    # fix launching from GUI
    sudo sed -i s^/usr/share/code/code^/usr/share/code/code\\ --disable-gpu^g /usr/share/applications/code.desktop

# Install Atom
    sudo apt-add-repository -y "ppa:webupd8team/atom"
    sudo apt-get update
    sudo apt-get install -y atom
    apm install nuclide ocaml-merlin language-ocaml
    # fix launching from GUI
    sudo sed -i s^/opt/atom/atom^/opt/atom/atom\\ --disable-gpu^g /usr/share/applications/atom.desktop

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
    sudo halt

  SHELL
end
