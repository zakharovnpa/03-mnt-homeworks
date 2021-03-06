## Ход выполнения ДЗ по теме "8.1. Введение в Ansible"

--------

# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению
1. Установите ansible версии 2.10 или выше.
```ps
root@server1:~# ansible --version
-bash: ansible: command not found
```
```ps
root@server1:~# pip3 install ansible
Collecting ansible
  Downloading ansible-5.3.0.tar.gz (38.0 MB)
     |████████████████████████████████| 38.0 MB 65 kB/s 
Collecting ansible-core~=2.12.2
  Downloading ansible-core-2.12.2.tar.gz (7.8 MB)
     |████████████████████████████████| 7.8 MB 726 kB/s 
Requirement already satisfied: PyYAML in /usr/lib/python3/dist-packages (from ansible-core~=2.12.2->ansible) (5.3.1)
Requirement already satisfied: cryptography in /usr/lib/python3/dist-packages (from ansible-core~=2.12.2->ansible) (2.8)
Collecting jinja2
  Downloading Jinja2-3.0.3-py3-none-any.whl (133 kB)
     |████████████████████████████████| 133 kB 8.6 MB/s 
Collecting packaging
  Downloading packaging-21.3-py3-none-any.whl (40 kB)
     |████████████████████████████████| 40 kB 5.4 MB/s 
Collecting resolvelib<0.6.0,>=0.5.3
  Downloading resolvelib-0.5.4-py2.py3-none-any.whl (12 kB)
Collecting MarkupSafe>=2.0
  Downloading MarkupSafe-2.0.1-cp38-cp38-manylinux2010_x86_64.whl (30 kB)
Collecting pyparsing!=3.0.5,>=2.0.2
  Downloading pyparsing-3.0.7-py3-none-any.whl (98 kB)
     |████████████████████████████████| 98 kB 4.7 MB/s 
Building wheels for collected packages: ansible, ansible-core
  Building wheel for ansible (setup.py) ... done
  Created wheel for ansible: filename=ansible-5.3.0-py3-none-any.whl size=63194604 sha256=bb4a4949104df11efe536aa3cf3f30c8a0285bdd58e0addc4272de93da457492
  Stored in directory: /root/.cache/pip/wheels/e4/99/b9/ef48a86add5258b2ad29f037ec9892f09ba27df769f15ec250
  Building wheel for ansible-core (setup.py) ... done
  Created wheel for ansible-core: filename=ansible_core-2.12.2-py3-none-any.whl size=2073804 sha256=eed5288334026d58c4d4533b10647591d29cb2660fe4c3f4b7fe78376b3272aa
  Stored in directory: /root/.cache/pip/wheels/99/88/72/24c0d37fb2e030bce4186f7bfae8694d2d862d344f9470155d
Successfully built ansible ansible-core
Installing collected packages: MarkupSafe, jinja2, pyparsing, packaging, resolvelib, ansible-core, ansible
Successfully installed MarkupSafe-2.0.1 ansible-5.3.0 ansible-core-2.12.2 jinja2-3.0.3 packaging-21.3 pyparsing-3.0.7 resolvelib-0.5.4
```
```ps
root@server1:~# ansible --version
ansible [core 2.12.2]
  config file = None
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.8/dist-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.8.10 (default, Nov 26 2021, 20:14:08) [GCC 9.3.0]
  jinja version = 3.0.3
  libyaml = True
root@server1:~# 

```
3. Создайте свой собственный публичный репозиторий на github с произвольным именем.
4. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

Перенесено в репозиторий [playbook](https://github.com/zakharovnpa/03-mnt-homeworks/tree/main/08-ansible-01-base/playbook)

## Основная часть
1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.
```yml
root@PC-Ubuntu:~/netology-project/mnt-homeworks-1/mnt-homeworks/08-ansible-01-base/playbook# ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
```yml
root@PC-Ubuntu:~/netology-project/mnt-homeworks-1/mnt-homeworks/08-ansible-01-base/playbook/group_vars/all#ls -l
итого 4
-rw-r--r-- 1 root root 19 фев  4 17:03 examp.yml

```
Содержимое файла
```yml
---
  some_fact: 12root
```

```yml
---
  some_fact: all default fact
```

```yml
root@PC-Ubuntu:~/netology-project/mnt-homeworks-1/mnt-homeworks/08-ansible-01-base/playbook# ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
```yml
root@server1:~# docker image ls
REPOSITORY                    TAG        IMAGE ID       CREATED         SIZE
postgres                      13         e01c76bb1351   10 days ago     371MB
postgres                      12         58bff7631346   4 weeks ago     371MB
dockerlesson                  1.0        24ef0db48b2f   5 weeks ago     85.3MB
zakharovnpa/debian            009        888b869de873   7 weeks ago     124MB
zakharovnpa/centos7           001        62ad6751e301   7 weeks ago     261MB
zakharovnpa/nginx             13.12.21   4b8b755d634a   7 weeks ago     141MB
zakharovnpa/ansible           8.12.21    06b6ca73286e   2 months ago    227MB
python                        3-alpine   eb5bc7d10d52   2 months ago    45.4MB
nginx                         latest     f652ca386ed1   2 months ago    141MB
debian                        latest     05d2318291e3   2 months ago    124MB
alpine                        3.14       0a97eee8041e   2 months ago    5.61MB
ubuntu                        latest     ba6acccedd29   3 months ago    72.8MB
hello-world                   latest     feb5d9fea6a5   4 months ago    13.3kB
centos                        7          eeb6ee3f44bd   4 months ago    204MB
centos                        latest     5d0da3dc9764   4 months ago    231MB
teikadev/postgres12-partman   latest     63db8bbf0186   24 months ago   155MB


```
```yml
root@server1:~# docker run -d --name ubuntu ba6acccedd29 sleep 60000000
c028217942579e6001eae90aa8bd8ce3d35f9c2896da4ccba94fb2c8c3558821
root@server1:~# 
root@server1:~# docker ps
CONTAINER ID   IMAGE          COMMAND            CREATED         STATUS         PORTS     NAMES
c02821794257   ba6acccedd29   "sleep 60000000"   7 seconds ago   Up 6 seconds             ubuntu
root@server1:~# 
root@server1:~# docker run -d --name centos7 eeb6ee3f44bd sleep 60000000
6a83d9d628c2bb2d008c1cb61932a05067691f7828c14df5bde8cee844728d37
root@server1:~# 
root@server1:~# 


```

```yml
root@server1:~# docker ps
CONTAINER ID   IMAGE          COMMAND            CREATED          STATUS          PORTS     NAMES
6a83d9d628c2   eeb6ee3f44bd   "sleep 60000000"   3 seconds ago    Up 2 seconds              centos7
c02821794257   ba6acccedd29   "sleep 60000000"   54 seconds ago   Up 53 seconds             ubuntu

```


4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.
File site.yml
```yml
---
  - name: Print os facts
    hosts: all
    tasks:
      - name: Print OS
        debug:
          msg: "{{ ansible_distribution }}"
      - name: Print fact
        debug:
          msg: "{{ some_fact }}"


```
File prod.yml
```yml
---
  el:
    hosts:
      centos7:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker

```
Вывод ` PLAY [Print os facts] ` 
```yml
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/hosts.yml site.yml 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "deb"
}
ok: [centos7] => {
    "msg": "el"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.

File group_vars/deb/example.yml
```yml
---
  some_fact: "deb default fact"

```
File group_vars/el/example.yml
```yml
---
  some_fact: "el default fact"

```

6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-vault encrypt group_vars/deb/examp.yml
New Vault password: 
Confirm New Vault password: 
Encryption successful
```
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-vault encrypt group_vars/el/examp.yml
New Vault password: 
Confirm New Vault password: 
Encryption successful

```

8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/hosts.yml site.yml 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************
ERROR! Attempting to decrypt but no vault secrets found

```
Запускаем плейбук с запросом пароля
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

control node – хост с предустановленным ansible. 
Найдем список плагинов для подключений.
```ps
root@server1:/usr/local/lib/python3.8/dist-packages/ansible/plugins/connection# ansible-doc -t connection -l
[WARNING]: Collection frr.frr does not support Ansible version 2.12.2
[WARNING]: Collection ibm.qradar does not support Ansible version 2.12.2
[WARNING]: Collection splunk.es does not support Ansible version 2.12.2
ansible.netcommon.httpapi      Use httpapi to run command on network appliances  
ansible.netcommon.libssh       (Tech preview) Run tasks using libssh for ssh connection 
ansible.netcommon.napalm       Provides persistent connection using NAPALM    
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol 
ansible.netcommon.network_cli  Use network_cli to run command on network appliances     
ansible.netcommon.persistent   Use a persistent unix socket for connection   
community.aws.aws_ssm          execute via AWS Systems Manager          
community.docker.docker        Run tasks in docker containers    
community.docker.docker_api    Run tasks in docker containers    
community.docker.nsenter       execute on host running controller container  
community.general.chroot       Interact with local chroot      
community.general.funcd        Use funcd to connect to target    
community.general.iocage       Run tasks in iocage jails        
community.general.jail         Run tasks in jails     
community.general.lxc          Run tasks in lxc containers via lxc python library  
community.general.lxd          Run tasks in lxc containers via lxc CLI         
community.general.qubes        Interact with an existing QubesOS AppVM      
community.general.saltstack    Allow ansible to piggyback on salt minions   
community.general.zone         Run tasks in a zone instance  
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt   
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines   
community.okd.oc               Execute tasks in pods running on OpenShift   
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools   
containers.podman.buildah      Interact with an existing buildah container   
containers.podman.podman       Interact with an existing podman container    
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes   
local                          execute on controller                       
paramiko_ssh                   Run tasks via python ssh (paramiko)      
psrp                           Run tasks over Microsoft PowerShell Remoting Protocol   
ssh                            connect via SSH client binary    
winrm                          Run tasks over Microsoft's WinRM 
```


Найдем список модулей для подключений.
Директория модулей ` /usr/local/lib/python3.8/dist-packages/ansible/modules `

```ps
ansible.netcommon.httpapi      Use httpapi to run command on network appliances 
ansible.netcommon.libssh       (Tech preview) Run tasks using libssh for ssh connection 
ansible.netcommon.napalm       Provides persistent connection using NAPALM 
ansible.netcommon.netconf      Provides a persistent connection using the netconf protocol 
ansible.netcommon.network_cli  Use network_cli to run command on network appliances     
ansible.netcommon.persistent   Use a persistent unix socket for connection              
community.aws.aws_ssm          execute via AWS Systems Manager                          
community.docker.docker        Run tasks in docker containers    
community.docker.docker_api    Run tasks in docker containers    
community.docker.nsenter       execute on host running controller container 
- community.general.chroot       Interact with local chroot           
community.general.funcd        Use funcd to connect to target 
community.general.iocage       Run tasks in iocage jails  
community.general.jail         Run tasks in jails      
community.general.lxc          Run tasks in lxc containers via lxc python library 
community.general.lxd          Run tasks in lxc containers via lxc CLI   
community.general.qubes        Interact with an existing QubesOS AppVM   
community.general.saltstack    Allow ansible to piggyback on salt minions 
community.general.zone         Run tasks in a zone instance      
community.libvirt.libvirt_lxc  Run tasks in lxc containers via libvirt   
community.libvirt.libvirt_qemu Run tasks on libvirt/qemu virtual machines 
community.okd.oc               Execute tasks in pods running on OpenShift  
community.vmware.vmware_tools  Execute tasks inside a VM via VMware Tools  
containers.podman.buildah      Interact with an existing buildah container 
containers.podman.podman       Interact with an existing podman container 
kubernetes.core.kubectl        Execute tasks in pods running on Kubernetes 
local                          execute on controller         
paramiko_ssh                   Run tasks via python ssh (paramiko)  
psrp                           Run tasks over Microsoft PowerShell Remoting Protocol  
ssh                            connect via SSH client binary    
winrm                          Run tasks over Microsoft's WinRM 
```
Наиболее подходящий плагин
```ps
local                          execute on controller    

```
Посмотреть описание плагина
```ps
ansible-doc -t connection local
```

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
Для добавления новой группы хостов в файле ` inventory/prod.yml ` добавлено:
```yml
  local:
    hosts:
      localhost:
        ansible_connection: local

```
Создана новая директория ` group_vars/local `
А в файле ` group_vars/local/examp.yml ` добавлено:
```yml
---
  some_fact: "local default fact"

```

11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "local default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.
```yml

```


## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-vault decrypt group_vars/deb/examp.yml
Vault password: 
Decryption successful
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# 
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-vault decrypt group_vars/el/examp.yml
Vault password: 
Decryption successful

```

2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-vault encrypt_string
New Vault password: 
Confirm New Vault password: 
Reading plaintext input from stdin. (ctrl-d to end input, twice if your content does not already have a newline)
PaSSw0rd
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          64643330363533656635636332643634323932653539303466326233386536383666396338306665
          3465623835666661663433303439346635303662326130300a333239663231666339646464653638
          63616565303434376664383737323731626538363530343866636432623838333338613139346464
          6635316338306263350a623864653266653630636532663961336134646261643662393465383537
          3238
Encryption successful
```
Добавляем полученное значение в `group_vars/all/exmp.yml`
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# cat group_vars/all/examp.yml 
---
  some_fact: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64643330363533656635636332643634323932653539303466326233386536383666396338306665
          3465623835666661663433303439346635303662326130300a333239663231666339646464653638
          63616565303434376664383737323731626538363530343866636432623838333338613139346464
          6635316338306263350a623864653266653630636532663961336134646261643662393465383537
          3238

```



4. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "PaSSw0rd"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

6. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот](https://hub.docker.com/r/pycontribs/fedora).

Запускаем новый контейнер fedore
```ps
root@server1:~# docker run -d --name fedore c31790496329 sleep 60000000
f44a0ef7cfe40e0933865ad73fc0007e9c5bdefc2525096cb46acddfaab98ee6
root@server1:~# 
root@server1:~# 
root@server1:~# docker ps
CONTAINER ID   IMAGE          COMMAND            CREATED         STATUS         PORTS     NAMES
f44a0ef7cfe4   c31790496329   "sleep 60000000"   3 seconds ago   Up 3 seconds             fedore
d81342e16c99   42a4e3b21923   "sleep 60000000"   21 hours ago    Up 21 hours              ubuntu
6a83d9d628c2   eeb6ee3f44bd   "sleep 60000000"   34 hours ago    Up 34 hours              centos7
```

Добавлена новая группа ` fed ` в ` inventory/prod.yml `
```yml
  fed:
    hosts:
      fedore:
        ansible_connection: docker
```
Добавлена новая переменная ` fed default fact ` в ` group_vars/fed/examp.yml `
```yml
---
  some_fact: "fed default fact"
```
Запускаем playbook
```ps
root@server1:~/learning-ansible/Lesson-ansible-01/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password: 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [localhost]
ok: [fedore]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [fedore] => {
    "msg": "Fedora"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "PaSSw0rd"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [fedore] => {
    "msg": "fed default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
fedore                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

7. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.

Составлен план действий скрипта:
- выполнить команду запуска контейнера ` centos7 `
- независимо от результата выполнения выполнить команду запуска контейнера ` ubuntu `
- независимо от результата выполнения выполнить команду запуска контейнера ` fedore `
- выдержать паузу 10 секунд
- выполнить команду запуска ` ansible-playbook `
- вывести в терминал список запущеных контейнеров.
- дождаться окончания выполнения команды запуска ` ansible-playbook `
- вывести в файл логов вывод команды ` ansible-playbook `
- выполнить команду остановки контейнера ` centos7 `
- независимо от результата выполнения выполнить команду остановки контейнера ` ubuntu `
- независимо от результата выполнения выполнить команду остановки контейнера ` fedore `
- вывести в терминал список запущеных контейнеров.

```bash
#!/usr/bin/env bash
#
# Скрипт для запуска контейнеров docker и запуска ansible-playbook

date >> ansible.log &&		# Запись даты выполнения скрипта
docker run -d --rm --name centos7 eeb6ee3f44bd sleep 60000000 && 	# Старт нового контейнера с именем centos7 на основе образа eeb6ee3f44bd
docker run -d --rm --name fedore c31790496329 sleep 60000000 &&		# Аналогично для fedore
docker run -d --rm --name ubuntu 42a4e3b21923 sleep 60000000 &&		# Аналогично для ubuntu

	sleep 3 &&				# Выполнить остановку скрипта на 3 секунды
	docker ps | grep 'Up' && 		# Вывод на терминал контейнеров, которые запустились
	sleep 10 &&				# Выполнить остановку скрипта на 10 секунд

ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass >> ansible.log && 	# Запуск playbook с запросом пароля на расшифровку файлов 
											# и запись результатов в файл ansible.log

docker stop centos7 &&		# Остановка и удаление уонтейнера с именем centos7
docker stop fedore &&		# Аналогично для fedore
docker stop ubuntu &&		# Аналогично для ubuntu

docker ps		# Вывод на терминал контейнеров, которые запустились

```
Листинг файла ` ansible.log `
```ps
Tue 08 Feb 2022 12:41:34 PM UTC

PLAY [Print os facts] **********************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]
ok: [fedore]
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ****************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}
ok: [fedore] => {
    "msg": "Fedora"
}

TASK [Print fact] **************************************************************
ok: [localhost] => {
    "msg": "PaSSw0rd"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}
ok: [fedore] => {
    "msg": "fed default fact"
}

PLAY RECAP *********************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
fedore                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

8. Все изменения должны быть зафиксированы и отправлены в вашей личный репозиторий.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

-----------



#### Хо выполнения Задания №1
