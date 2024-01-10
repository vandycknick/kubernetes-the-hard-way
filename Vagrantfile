controllers=3
workers=3

Vagrant.configure("2") do |config|

  (0..controllers - 1).each do |n|
    config.vm.define "controller-#{n}" do |controller|
      controller.vm.box = "ubuntu/focal64"
      controller.vm.box_version = "20231207.0.0"
      controller.vm.hostname = "controller-#{n}"
  
      controller.vm.network :private_network, ip: "10.240.0.1#{n}", auto_config: true
  
      controller.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 2
      end
    end
  end

  (0..workers - 1).each do |n|
    config.vm.define "worker-#{n}" do |worker|
      worker.vm.box = "ubuntu/focal64"
      worker.vm.box_version = "20231207.0.0"
      worker.vm.hostname = "worker-#{n}"
  
      worker.vm.network :private_network, ip: "10.240.0.2#{n}", auto_config: true
  
      worker.vm.provider "virtualbox" do |v|
          v.memory = 2048
          v.cpus = 2
      end
    end
  end

end
