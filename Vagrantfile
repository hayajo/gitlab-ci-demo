# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.define "server" do |server|
    SERVER_ADDR = "192.168.133.10"
    server.vm.box = "boxcutter/centos66"
    server.vm.network "forwarded_port", guest: 80, host: 8081
    server.vm.network "private_network", ip: SERVER_ADDR
    server.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
    end
    server.vm.provision "shell", inline: <<-SHELL
      sudo yum -y install curl openssh-server postfix cronie
      sudo service postfix start
      sudo chkconfig postfix on
      sudo lokkit -s http -s ssh
      curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
      sudo yum -y install gitlab-ce
      sudo sed -i.bak -e "s/^\\(external_url \\)'http:\\/\\/localhost'/\\1'http:\\/\\/#{SERVER_ADDR}'/" /etc/gitlab/gitlab.rb
      sudo gitlab-ctl reconfigure
    SHELL
  end

  config.vm.define "runner" do |runner|
    runner.vm.box = "boxcutter/centos71"
    runner.vm.network "private_network", ip: "192.168.133.200"
    runner.vm.provision "shell", inline: <<-SHELL
      curl -sSL https://get.docker.com/ | sh
      curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash
      sudo yum -y install git gitlab-ci-multi-runner
      sudo yum -y install perl perl-core
      sudo wget -O - https://cpanmin.us | perl - --sudo App::cpanminus
    SHELL
  end

end
