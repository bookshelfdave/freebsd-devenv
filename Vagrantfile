# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox"
  config.vm.box = "generic/freebsd12"
  config.vm.guest = :freebsd
  config.ssh.shell = "sh"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provision "shell", inline: <<-SHELL
    sudo su -
    echo ">>>> FreeBSD update"
    freebsd-update fetch --not-running-from-cron
    freebsd-update install --not-running-from-cron

    cat << EOF >> /etc/rc.conf
ntpd_enable=YES
ntpdate_enable="YES"
EOF
    echo ">>>> Portsnap"
    portsnap fetch extract update
    
    echo ">>>> Pkg"
    pkg update
    pkg upgrade -y
    pkg install -y bash git neovim wget curl
    export VERSION=$(uname -r | awk -F"-" '{ print $1 }')
    svnlite checkout https://svn.freebsd.org/base/releng/${VERSION} /usr/src

    curl -o /root/.vimrc https://raw.githubusercontent.com/metadave/dotfiles/master/.vimrc

    mkdir -p /root/.config/nvim/
    cat << EOF > /root/.config/nvim/init.vim
set runtimepath^=~/.vim runtimepath+=~/.vim/after
let &packpath = &runtimepath
source ~/.vimrc
EOF
  echo "FreeBSD setup complete!"
  SHELL

    config.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "8196"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
      vb.customize ["modifyvm", :id, "--audio", "none"]
      vb.customize ["modifyvm", :id, "--nictype1", "virtio"]
      vb.customize ["modifyvm", :id, "--nictype2", "virtio"]
  end
end
