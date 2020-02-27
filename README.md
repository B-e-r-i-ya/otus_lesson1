# *otus_lesson1

# Этап первый

Создаем `vagrantfile` по средствам которого создается машина с именем `node1`
двух ядерным процессором и 1024 ОЗУ:

```
# _*_ mode: ruby _*_
# vi: set ft=ruby
Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
....
    config.vm.define "node1" do|node1|
<------>node1.vm.hostname = "node1"
....
<------>config.vm.provider "virtualbox" do |node1|
<------>    node1.name = "node1"
<------>    node1.cpus = 2
<------>    node1.memory = "1024"
<------>end

    node1.vm.provision "shell", path: "script.sh"

    end
end
```

после создания машины выполняется скрипт 
который производит обновление ядра системы:

```
#!/bin/bash
uname -r
yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
yum --enablerepo elrepo-kernel install kernel-ml -y
grub2-mkconfig -o /boot/grub2/grub.cfg
grub2-set-default 0
reboot
```

Выполняем команду:
```
root@Proffff:~/otus/lesson1# vagrant up
```
по окончании виртуальная машина перезагрузится



Ожидаем пару минут загрузки системы
Выполняем команду 
```
vagrant ssh
```

Внутри виртуальной машину выполняем команду 
```
[vagrant@node1 ~]$ uname -r
5.5.6-2.el7.elrepo.x86_64
```
Убедившись что ядро обновленно переходим к следующиму этапу...


# Этап Второй

Следущими действия необходимо очистить образ от лишней информации и создать
новый бокс который будет загружен в `Vagrant Cloud`

# Очистка

внутри виртуальной нужно выполнить скрипт `stage-2-clean.sh`


```
#!/bin/bash

# clean all
yum update -y
yum clean all


# Install vagrant default key
mkdir -pm 700 /home/vagrant/.ssh
curl -sL https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub -o /home/vagrant/.ssh/authorized_keys
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant:vagrant /home/vagrant/.ssh


# Remove temporary files
rm -rf /tmp/*
rm  -f /var/log/wtmp /var/log/btmp
rm -rf /var/cache/* /usr/share/doc/*
rm -rf /var/cache/yum
rm -rf /vagrant/home/*.iso
rm  -f ~/.bash_history
history -c

rm -rf /run/log/journal/*

# Fill zeros all empty space
dd if=/dev/zero of=/EMPTY bs=1M
rm -f /EMPTY
sync
grub2-set-default 1
echo "###   Hi from secone stage" >> /boot/grub2/grub.cfg
poweroff
```

После очистки виртуальная машина выключиться, можно приступать к созданию бокса.
Выполним команду `vagrant package node1`, в следствии ее выполнения в папке с проектом появиться файл `package.box`
Полученный бокс необходимо загрузить в `Vagrant cloud`
выполнив команду  `vagrant cloud publish --release B-e-r-i-ya/centos-7-5 1.0 virtualbox package.box `


Создаем новый `Vagrantfile` для данного бокса

```
# _*_ mode: ruby _*_
# vi: set ft=ruby
Vagrant.configure("2") do |config|
    config.vm.box = "B-e-r-i-ya/centos-7-5"

    config.vm.define "node1" do|node1|
	node1.vm.hostname = "node1"

	config.vm.provider "virtualbox" do |node1|
	    node1.name = "node1"
	    node1.cpus = 2
	    node1.memory = "1024"
	end

    end
end
```


