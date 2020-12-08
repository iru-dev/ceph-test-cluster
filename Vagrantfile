nodes = [ 
  { :hostname => 'mon1', :ip => '172.30.0.2'}, 
  { :hostname => 'mon2', :ip => '172.30.0.3'}, 
  { :hostname => 'mon3', :ip => '172.30.0.4'}, 
  { :hostname => 'osd1',  :ip => '172.30.0.5', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'osd2',  :ip => '172.30.0.6', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'osd3',  :ip => '172.30.0.7', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'osd4',  :ip => '172.30.0.8', :ram => 1024, :osd => 'yes' } 
] 
 
Vagrant.configure("2") do |config| 
  nodes.each do |node| 
    config.vm.define node[:hostname] do |nodeconfig| 
      nodeconfig.vm.box = "centos/7" 
      #nodeconfig.vm.box = "geerlingguy/centos7" 
      nodeconfig.vm.hostname = node[:hostname] 
      nodeconfig.vm.network :private_network, ip: node[:ip] 
      memory = node[:ram] ? node[:ram] : 512; 
      nodeconfig.vm.provider :virtualbox do |vb| 
        vb.customize [ 
          "modifyvm", :id, 
          "--memory", memory.to_s, 
        ] 
        if node[:osd] == "yes"         
        disk = "disk-#{node[:hostname]}"
        unless FileTest.exist?(disk)
            vb.customize [ 'createhd', '--filename', disk, '--size', 5*1024 ]
        end
          vb.customize ["storagectl", :id, "--name", "SATA Controller", "--add", "sata"]
          vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 1, "--type", "hdd", "--medium", "disk-#{node[:hostname]}.vdi"]
        end
      end 
        nodeconfig.vm.provision 'shell', inline: "echo ~/.ssh/id_ecdsa.pub >> /home/vagrant/.ssh/authorized_keys", privileged: false
        nodeconfig.vm.provision 'shell', inline: "hostname ceph-#{node[:hostname]};echo ceph-#{node[:hostname]} > /etc/hostname"
    end 
    config.hostmanager.enabled = true 
    config.hostmanager.manage_guest = true 
  end 
end
