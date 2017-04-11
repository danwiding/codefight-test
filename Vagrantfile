Vagrant.configure('2') do |config|

  hostname = 'ruby.box'
  static_ip = '10.42.42.42'

  # Box
  config.vm.box = 'hashicorp/precise64'
  config.vm.hostname = hostname

  config.vm.network :forwarded_port, guest: 3000, host: 3000, auto_correct: true
  config.vm.network :forwarded_port, guest: 5432, host: 5432, auto_correct: true
  config.vm.network :private_network, ip: static_ip

  # config.vm.synced_folder '.', '/vagrant', nfs: true

  config.ssh.forward_agent = true

  config.vm.provider 'virtualbox' do |vb|
    vb.gui = false
    vb.customize [
      'modifyvm', :id,
      '--accelerate3d', 'on',
      '--memory', ENV['VAGRANT_RAM_MB'] || '4096',
      '--cpus', ENV['VAGRANT_CPU_COUNT'] || '3',
      '--nictype1', 'virtio',
      '--nictype2', 'virtio',
      '--natdnshostresolver1', 'on',
      '--natdnsproxy1', 'on',
      '--vram', '256',
      '--graphicscontroller', 'vboxvga',
      '--ioapic', 'on',
      '--hwvirtex', 'on'
    ]
  end

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ['cookbooks']
    chef.add_recipe 'apt'
    chef.add_recipe 'heroku-toolbelt'
    chef.add_recipe 'nodejs'
    chef.add_recipe 'oh_my_zsh'
    chef.add_recipe 'postgresql::client'
    chef.add_recipe 'ruby_build'
    chef.add_recipe 'ruby_rbenv::user'
    chef.add_recipe 'sysstat'
    chef.add_recipe 'vim'

    ruby_version = File.read('.ruby-version')

    chef.json = {
      oh_my_zsh: {
        users: [{
          login: 'vagrant',
          plugins: [''],
        }
        ]
      },
      postgresql: {
        version: '9.4'
      },
      rbenv: {
        user_installs: [{
          user: 'vagrant',
          rubies: [ruby_version],
          global: ruby_version
        }
        ]
      }
    }
  #       ,
  #   gems: {
  #     ruby_version: [
  #       { name: 'bundler' }
  #     ]
  #   }
  end
  config.vm.provision :shell, inline: "sudo apt-get update"
  config.vm.provision :docker
  config.vm.provision :docker_compose, yml: '/vagrant/docker-compose.yml', run: 'always'
end
