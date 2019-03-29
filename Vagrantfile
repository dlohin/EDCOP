$script = <<SCRIPT
VERSION='2.1'
RELEASE=1


#############Initial Setup of Build environment#################
sudo yum -y install  epel-release

sudo yum -y install golang glide rpm-build git gcc kernel-headers wget install xmlto krb5-server pcp-libs-devel systemd-devel glib2-devel glib-networking docbook-style-xsl libxslt-devel krb5-devel zlib-devel openssl-devel libssh-devel intltool automake autoconf pam-devel pkgconfig json-glib json-glib-devel polkit-devel


mkdir -p ~/rpmbuild/SOURCES


##########Build out of CNI packages###########################

cd /tmp

git clone https://github.com/intel/multus-cni/; cd ./multus-cni; git checkout -b tags/v3.2; ./build

cd /tmp

wget https://github.com/intel/sriov-cni/archive/v1.0.0.tar.gz -O sriov-cni.tgz; tar xvzf sriov-cni.tgz; cd ./sriov-cni-*; make

cd /tmp

wget https://github.com/containernetworking/plugins/archive/v0.7.5.tar.gz -O plugins.tgz; tar xvzf plugins.tgz;  cd ./plugins-*; ./build.sh

cd /tmp

mkdir edcop-cni-$VERSION

cp multus-cni/bin/multus edcop-cni-$VERSION

cp plugins-*/bin/* edcop-cni-$VERSION

cp sriov-cni-*/build/* edcop-cni-$VERSION

tar cvzf edcop-cni-$VERSION.tar.gz edcop-cni-$VERSION

cp edcop-cni-$VERSION.tar.gz /root/rpmbuild/SOURCES/edcop-cni.tar.gz


rpmbuild -bb /vagrant/ext-packages/edcop-cni/edcop-cni.spec


########Build Cockpit###############
wget https://github.com/cockpit-project/cockpit/releases/download/190/cockpit-190.tar.xz -O /root/rpmbuild/SOURCES/cockpit-190.tar.xz



SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.network "private_network", ip: "192.168.1.4"
  config.vm.define "EDCOP_KS_DEV"
  config.disksize.size='50GB'
  #config.vm.provision "file", source: "ext-packages/", destination: "/vagrant"
  config.vm.provision "shell", inline: $script
  
  
  config.vm.provider "virtualbox" do |v|
    v.name = "EDCOP_KS_DEV"
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--name", "EDCOP_KS_DEV"]
    v.customize ["modifyvm", :id, "--memory", 8192]
    v.customize ["modifyvm", :id, "--cpus", 4]
  end  
end

