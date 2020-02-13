$script = <<-SCRIPT
#!/bin/bash
apt-get update
apt-get -y install curl

# create staging directories
if [ ! -d /drop ]; then
  mkdir /drop
fi
if [ ! -d /downloads ]; then
  mkdir /downloads
fi

# download the Chef server package
if [ ! -f /downloads/chef-server-core_13.1.13-1_amd64.deb ]; then
  echo "Downloading the Chef server package..."
  wget -nv -P /downloads https://packages.chef.io/files/stable/chef-server/13.1.13/ubuntu/16.04/chef-server-core_13.1.13-1_amd64.deb
fi

# install Chef server
if [ ! $(which chef-server-ctl) ]; then
  echo "Installing Chef server..."
  dpkg -i /downloads/chef-server-core_13.1.13-1_amd64.deb
  chef-server-ctl reconfigure --chef-license=accept

  echo "Waiting for services..."
  until (curl -D - http://localhost:8000/_status) | grep "200 OK"; do sleep 15s; done
  while (curl http://localhost:8000/_status) | grep "fail"; do sleep 15s; done

  echo "Creating initial user and organization..."
  chef-server-ctl user-create chefadmin Chef Admin admin@4thcoffee.com insecurepassword --filename /drop/chefadmin.pem
  chef-server-ctl org-create 4thcoffee "Fourth Coffee, Inc." --association_user chefadmin --filename 4thcoffee-validator.pem
fi

echo "Your Chef server is ready!"
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.define 'chef13', primary: true do |chef13|
    chef13.vm.network "forwarded_port", guest: 80, host: 8080
    chef13.vm.hostname = 'dev-chef13-linux'
    chef13.vm.network :private_network, ip: '33.33.33.30'
    chef13.vm.box = "ubuntu/xenial64"
    chef13.vm.provider 'virtualbox' do |vb|
      vb.gui = false
      vb.customize ['modifyvm', :id, '--memory', '3584']
    end

    chef13.vm.provision "shell", inline: $script
  end
  config.vm.define 'linuxnode1', primary: false do |linuxnode1|
      linuxnode1.vm.box = "ubuntu/xenial64"
      linuxnode1.vm.network :private_network, ip: '33.33.33.31'
  end

end
