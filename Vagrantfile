Vagrant.configure(2) do |config|
  config.vm.box = "geerlingguy/centos7"

  #Dynamically set the memory to 2GB or 1/4 of the machine memory
  config.vm.provider "virtualbox" do |v|
    host = RbConfig::CONFIG['host_os']

    # Give VM 1/4 system memory
    if host =~ /darwin/
    # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024
    elsif host =~ /linux/
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i
    elsif host =~ /mswin|mingw|cygwin/
      # Windows code via https://github.com/rdsubhas/vagrant-faster
      mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024
    end

    mem = mem / 1024 / 4
    mem = 2048 if mem > 2048

    v.customize ["modifyvm", :id, "--memory", mem]
  end

  config.vm.hostname = "tulvivo"
  config.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct: true

  # Cache installing items. Reference: http://fgrehm.viewdocs.io/vagrant-cachier/
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.host_key_checking = false
    ansible.inventory_path = "inventory/vagrant/"
    ansible.limit = "all"
    ansible.raw_arguments = ["--vault-password-file=~/.vault"]
  end
end
