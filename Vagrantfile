VAGRANT_COMMAND = ARGV[0]

Vagrant.configure(2) do |config|

  config.vm.box = "bento/ubuntu-16.04"
  config.vm.hostname = "mosster"
  config.vm.synced_folder ".", "/home/vagrant/go/src/mosster/", create: true

  if Vagrant.has_plugin?("vagrant-timezone")
    config.timezone.value = :host
  end

  config.vm.provider "virtualbox" do |v|
    v.name = "mosster Development"
    v.cpus = "4"
    v.memory = "4096"
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
    v.customize ["modifyvm", :id, "--usb", "off"]
    v.customize ["modifyvm", :id, "--usbehci", "off"]
    v.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end

  $script =<<DONE
APT_FLAGS="-qq -y -o Dpkg::Use-Pty=0"
export DEBIAN_FRONTEND=noninteractive

# Upgrade apt-get packages, install Java and some basic tools
apt-get $APT_FLAGS update
apt-get -qq -o Dpkg::Use-Pty=0 -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" upgrade
apt-get $APT_FLAGS install tmux bash-completion man vim default-jre

# Install Go
GO_VERSION=1.8.1
wget -nv https://storage.googleapis.com/golang/go$GO_VERSION.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go$GO_VERSION.linux-amd64.tar.gz
rm go$GO_VERSION.linux-amd64.tar.gz
mkdir -p /home/vagrant/go/bin
chown vagrant:vagrant -R /home/vagrant/go/
echo "export GOPATH=/home/vagrant/go/" >> /home/vagrant/.bashrc
echo "export PATH=$PATH:$GOPATH/bin:/usr/local/go/bin" >> /home/vagrant/.bashrc

# Install ANTLRv4
wget -nv http://www.antlr.org/download/antlr-4.5.3-complete.jar
mv antlr-4.5.3-complete.jar /usr/local/lib/
echo "export CLASSPATH=".:/usr/local/lib/antlr-4.5.3-complete.jar:$CLASSPATH"" >> /home/vagrant/.bashrc
echo "alias antlr4='java -Xmx500M -cp "/usr/local/lib/antlr-4.5.3-complete.jar:$CLASSPATH" org.antlr.v4.Tool'" >> /home/vagrant/.bashrc

touch /home/vagrant/.hushlogin
DONE

  config.vm.provision "shell", "inline": $script

end

# vim: ts=2:sw=2:et:ft=ruby
