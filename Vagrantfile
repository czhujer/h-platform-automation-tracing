# -*- mode: ruby -*-
# vi: set ft=ruby :

#in case you have problem(s) with "vagrant up", use main vagrantfile in repo h-platform-automation-infrastructure

Vagrant.configure(2) do |config|
  config.vm.define :"tracing-stack" do |config_tracing|
    config_tracing.vm.box = "peru/ubuntu-20.04-server-amd64"
    config_tracing.vm.hostname = "tracing-stack"
    config_tracing.vm.define "tracing-stack"

    dir_tracing = File.expand_path("..", __FILE__)
    puts "DIR_tracing: #{dir_tracing}"

    config_tracing.vm.synced_folder dir_tracing, '/vagrant', type: 'sshfs'

    config_tracing.vm.provision "docker-install", type: "shell", path: File.join(dir_tracing,'scripts/bootstrap-docker.sh'), privileged: false

    config_tracing.vm.provision "compose-files",
      type: "shell",
      :inline => "mkdir -p /etc/docker/compose/tracing-stack && cp /vagrant/docker-files/tracing-stack.yaml /etc/docker/compose/tracing-stack/docker-compose.yaml",
      :privileged => true

    config_tracing.vm.provision "compose-exec",
      type: "shell",
      path: File.join(dir_tracing,'scripts/docker-compose-exec.sh'),
      :privileged => true

  #  config.vm.provision "bootstrap-wordpress",
  #    type: "shell",
  #    path: File.join(dir,'scripts/bootstrap-wordpress.sh'),
  #    :privileged => true
  #
  #  config.vm.provision "bootstrap-wordpress-plugins",
  #    type: "shell",
  #    path: File.join(dir,'scripts/bootstrap-wordpress-plugins.sh'),
  #    :privileged => true

$script_status = <<SCRIPT3
echo "Showing status of tracing-stack compose..."
cd /etc/docker/compose/tracing-stack && docker-compose ps
SCRIPT3

    config_tracing.vm.provision "status", type: "shell", inline: $script_status, privileged: false

    # Expose http/s port
    config_tracing.vm.network "forwarded_port", guest: 8081, host: 8080, auto_correct: true

    config_tracing.vm.provider :libvirt do |v|
      v.memory = 1024
      v.cpus = 2
    end
  end
end
