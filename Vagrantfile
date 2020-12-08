nodes = [ 
  { :hostname => 'ceph-mon1', :ip => '172.30.0.2'}, 
  { :hostname => 'ceph-mon2', :ip => '172.30.0.3'}, 
  { :hostname => 'ceph-mon3', :ip => '172.30.0.4'}, 
  { :hostname => 'ceph-osd1',  :ip => '172.30.0.5', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'ceph-osd2',  :ip => '172.30.0.6', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'ceph-osd3',  :ip => '172.30.0.7', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'ceph-osd4',  :ip => '172.30.0.8', :ram => 1024, :osd => 'yes' }, 
  { :hostname => 'ceph-monitoring',  :ip => '172.30.0.9', :ram => 1024 } 
] 
 
Vagrant.configure("2") do |config| 
  nodes.each do |node| 
    config.vm.define node[:hostname] do |nodeconfig| 
      nodeconfig.vm.box = "centos/8" 
      #nodeconfig.vm.box = "geerlingguy/centos7" 
      nodeconfig.vm.hostname = node[:hostname] 
      
      nodeconfig.vm.network :private_network, ip: node[:ip] 
      #nodeconfig.vm.network :public_network, ip: node[:ip]
      memory = node[:ram] ? node[:ram] : 512; 
      nodeconfig.vm.provider :virtualbox do |vb| 
        vb.customize [ 
          "modifyvm", :id, 
          "--memory", memory.to_s, 
        ] 
        if node[:osd] == "yes"         
        disk = "disk-#{node[:hostname]}"
        unless FileTest.exist?(disk)
            vb.customize [ 'createhd', '--filename', disk, '--size', 50*1024 ]
        end
          vb.customize ["storagectl", :id, "--name", "SATA Controller", "--add", "sata"]
          vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 1, "--type", "hdd", "--medium", "disk-#{node[:hostname]}.vdi"]
        end
      end 
      ssh_pub_key = File.readlines("/home/iru/.ssh/id_ecdsa.pub").first.strip

        nodeconfig.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
        nodeconfig.vm.provision 'shell', inline: "hostname #{node[:hostname]};echo #{node[:hostname]} > /etc/hostname"
    end 
    config.hostmanager.enabled = true  
    config.hostmanager.manage_guest = true 
  end 
end
