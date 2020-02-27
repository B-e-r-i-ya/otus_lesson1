## **otus_lesson1**

# **Этап первый**

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

