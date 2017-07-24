# -*- mode: ruby -*-

Vagrant.configure(2) do |config|
  config.vm.box = "aws"
  config.ssh.username = "ubuntu"
  config.vm.synced_folder ".", disabled: true
  
  N = 2
    (1..N).each do |node_id|

      config.vm.define "node#{node_id}" do |node|
        node.vm.provider :aws do |aws, override|
           aws.access_key_id = ENV['AWS_ACCESS_KEY']
           aws.secret_access_key = ENV['AWS_SECRET_KEY']
           aws.region = "us-west-2"
           aws.availability_zone = "us-west-2c"
           aws.subnet_id = "subnet-3bdd1e60"
           aws.associate_public_ip = true
           aws.ami = "ami-d94f5aa0"
           aws.keypair_name = ENV['AWS_PRIVATE_KEY_NAME']
           aws.instance_type = "t2.micro"
           aws.security_groups = ["sg-05c97a7f"]
           aws.block_device_mapping = [
              {
                'DeviceName' => "/dev/sda1",
                'Ebs.DeleteOnTermination' => true
              }
           ]

          override.ssh.private_key_path = ENV['AWS_PRIVATE_KEY']
        end

        node.vm.hostname = "node#{node_id}"
        if node_id == N
          node.vm.provision :ansible do |ansible|
            ansible.limit = "all,localhost"
            ansible.playbook = "site.yml"
            ansible.groups = {
              "gerrit"       => ["node1"],
              "jenkins"       => ["node2"],
              "all:children" => ["jenkins, gerrit"],
            }
            ansible.sudo = true
          end
        end
      end
  end
end
