### Ход выполнения ДЗ по теме "8.4 Работа с Roles"

## Домашнее задание к занятию "8.4 Работа с Roles" - Захаров Сергей Николаевич

## Подготовка к выполнению
1. Создайте два пустых публичных репозитория в любом своём проекте: kibana-role и filebeat-role.
2. Добавьте публичную часть своего ключа к своему профилю в github.

Локальная директория на учебном ПК `root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible# `

## Основная часть

Наша основная цель - разбить наш playbook на отдельные roles. 
Задача: 
* сделать roles для:
  - elastic, 
  - kibana, 
  - filebeat и 
* написать playbook для использования этих ролей. 

Ожидаемый результат: 
- существуют:
  - два ваших репозитория с roles и 
  - один репозиторий с playbook.

1. Создать в старой версии playbook файл `requirements.yml` и заполнить его следующим содержимым:
   ```yaml
   ---
     - src: git@github.com:netology-code/mnt-homeworks-ansible.git
       scm: git
       version: "2.1.4"
       name: elastic 
   ```
2. При помощи `ansible-galaxy` скачать себе эту роль.
* Скачать себе - это значит в свой локальный репозиторий, где расположен `ansible-playbook`, запустить команду 
  - `ansible-galaxy install -r requirements.yml -p elasticsearch_role`
* Скачивание произойдет с удаленного репозитрия на основании данный в файле `requirements.yml`

```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible# ansible-galaxy install -r requirements.yml -p roles
Starting galaxy role install process
- extracting elastic to /root/ansible-learning/yandex-cloud/Dzeta/ansible/roles/elastic
- elastic (2.1.4) was installed successfully
```
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible# ansible-galaxy role list /root/ansible-learning/yandex-cloud/Dzeta/ansible/roles/elastic/
# /root/ansible-learning/yandex-cloud/Dzeta/ansible/roles/elastic
- /root/ansible-learning/yandex-cloud/Dzeta/ansible/roles/elastic/, 2.1.4

```


* Примеры установки, удаления ролей с помощью `ansible-galaxy`

 ```ps
 root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Epsilon/Galaxy# ansible-galaxy install -r requirements.yml -p elasticsearch_role
Starting galaxy role install process
- extracting elastic to /root/ansible-learning/yandex-cloud/Galaxy/roles/elastic
- elastic (2.1.4) was installed successfully

 ```
 
 ```ps
 root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list elastic
# /root/.ansible/roles
- elastic, 2.1.4
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list kibana_role
[WARNING]: - the role kibana_role was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list kibana_roles
[WARNING]: - the role kibana_roles was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list kibana_role
[WARNING]: - the role kibana_role was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# 
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy remove elastic
- successfully removed elastic
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list elastic
[WARNING]: - the role elastic was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy install -r requirements.yml -p roles
Starting galaxy role install process
- extracting elastic to /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic
- elastic (2.1.4) was installed successfully
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list elastic
[WARNING]: - the role elastic was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic
# /root/ansible-learning/yandex-cloud/Alfa/ansible/roles
- /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic, 2.1.4
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy remove /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic
- successfully removed /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic
 ```
 
 ```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Alfa/ansible# ansible-galaxy role list /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic
[WARNING]: - the role /root/ansible-learning/yandex-cloud/Alfa/ansible/roles/elastic was not found
[WARNING]: - the configured path /usr/share/ansible/roles does not exist.
[WARNING]: - the configured path /etc/ansible/roles does not exist.

 ```
 
 
3. Создать новый каталог с ролью при помощи `ansible-galaxy role init kibana-role`.
* Перейти в каталог `roles` и запустить команду. Все каталоги для новой роли будут созданы здесь.

```ps
 root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# 
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# ansible-galaxy role init kibana
- Role kibana was created successfully
```
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# 
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# ansible-galaxy role init filebeat
- Role filebeat was created successfully
```
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# ls -l
итого 12
drwxr-xr-x 10 root root 4096 фев 23 15:43 elastic
drwxr-xr-x 10 root root 4096 фев 23 16:02 filebeat
drwxr-xr-x 10 root root 4096 фев 23 16:02 kibana
```
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Dzeta/ansible/roles# tree
.
├── elastic
│   ├── defaults
│   │   └── main.yml
│   ├── handlers
│   │   └── main.yml
│   ├── Jenkinsfile
│   ├── meta
│   │   └── main.yml
│   ├── molecule
│   │   ├── alternative
│   │   │   ├── converge.yml
│   │   │   ├── molecule.yml
│   │   │   └── verify.yml
│   │   └── default
│   │       ├── converge.yml
│   │       ├── files
│   │       │   └── empty
│   │       ├── molecule.yml
│   │       └── verify.yml
│   ├── README.md
│   ├── tasks
│   │   ├── configure.yml
│   │   ├── download_apt.yml
│   │   ├── download_yum.yml
│   │   ├── install_apt.yml
│   │   ├── install_yum.yml
│   │   ├── main.yml
│   │   └── precheck.yml
│   ├── templates
│   │   └── elasticsearch.yml.j2
│   ├── test-requirements.txt
│   ├── tests
│   │   ├── inventory
│   │   └── test.yml
│   ├── tox.ini
│   └── vars
│       └── main.yml
├── filebeat
│   ├── defaults
│   │   └── main.yml
│   ├── files
│   ├── handlers
│   │   └── main.yml
│   ├── meta
│   │   └── main.yml
│   ├── README.md
│   ├── tasks
│   │   └── main.yml
│   ├── templates
│   ├── tests
│   │   ├── inventory
│   │   └── test.yml
│   └── vars
│       └── main.yml
└── kibana
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml

30 directories, 41 files

 ```

4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 


Разбираем файл `site.yml` плейбука на отдельные файлы

* `/roles/kibana/handler/main.yml`

```yml
---
# handlers file for kibana

  - name: restart Elasticsearch
    become: true
    service:
      name: elasticsearch
      state: restarted

```
* `/roles/kibana/tasks/main.yml`

```yml
  - import_tasks: "configure.yml"
    tags: [config]                                       
  - include_tasks: "download_kibana_rpm.yml" 
    tags: [install]
  - include_tasks: "install_kibana.yml" 
    tags: [install]
```
* `/roles/kibana/tasks/configure.yml`
```yml
---

    - name: Configure Kibana
      become: true
      template:
        src: kibana.yml.j2
        dest: /etc/kibana/kibana.yml
      notify: restart kibana


```
* `/roles/kibana/tasks/download_kibana_rpm.yml`
```yml
---

- name: "Download Kibana's rpm"
      get_url:
        url: "https://artifacts.elastic.co/downloads/kibana/kibana-{{ elk_stack_version }}-x86_64.rpm"
        dest: "/tmp/kibana-{{ elk_stack_version }}-x86_64.rpm"
      register: download_kibana
      until: download_kibana is succeeded

```
* `/roles/kibana/tasks/install_kibana.yml`
```yml
---

    - name: Install Kibana
      become: true
      yum:
        name: "/tmp/kibana-{{ elk_stack_version }}-x86_64.rpm"
        state: present
      notify: restart kibana

```
* `/roles/kibana/tasks/main.yml` - пока это пример
```yml
  - import_tasks: "configure.yml"
  - include_tasks: "download {{ ansible_facts.pkg_mgr }}.yml"     # пока это пример
    tags: [always]
                                                               
  - include_tasks: "download_kibana_rpm.yml"    # пока это пример
    # when:
    #  - force_reinstall
    # tags: [install]
  - include_tasks: "install_kibana.yml"   # пока это пример
    # when:
    #  - skip_instal is undefinde
    #  - ansible_distributon in support_system
    tags: [install]
    
    tags: [config]
    
    
    tags: [service]
```

* Разнесим переменные между `vars` и `default`.

`/roles/kibana/default/main.yml`
```yml
---
# defaults file for kibana
#
#

elk_stack_version: "7.14.0"

kibana_version: "7.14.0"

```
5. Перенести нужные шаблоны конфигов в `templates`.
* `/roles/kibana/templates/kibana.yml.j2`
```ps
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://{{ hostvars['el-instance']['ansible_facts']['default_ipv4']['address'] }}:9200"]
kibana.index: ".kibana"

```
6. Создать новый каталог с ролью при помощи `ansible-galaxy role init filebeat-role`.
* Перейти в каталог `roles` и запустить команду. Все каталоги для новой роли будут созданы здесь.
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Galaxy/roles# ansible-galaxy role init filebeat_role
- Role filebeat_role was created successfully

```
* `/roles/filebeat/tasks/main.yml`
```yml
  - import_tasks: "configure_filebeat.yml"
    tags: [config]                                       
  - include_tasks: "download_filebeat_rpm.yml" 
    tags: [install]
  - include_tasks: "install_filebeat.yml" 
    tags: [install]
  - include_tasks: "set_filebeat_systemwork.yml" 
    tags: [install]
  - include_tasks: "load_kibana_dashboard.yml" 
    tags: [install]
```

```yml
configure_filebeat.yml  
download_filebeat_rpm.yml  
install_filebeat.yml  
load_kibana_dashboard.yml  
main.yml  
set_filebeat_systemwork.yml
```



7. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 

```ps

```
8. Перенести нужные шаблоны конфигов в `templates`.

```ps

```
9. Описать в `README.md` обе роли и их параметры.

```ps

```
10. Выложите все roles в репозитории. Проставьте тэги, используя семантическую нумерацию.

```ps

```
11. Добавьте roles в `requirements.yml` в playbook.

```yml
   ---
     - src: git@github.com:
       scm: git
       version: "2.1.4"
       name: elastic 
     - src: git@github.com:
       scm: git
       version: "2.1.4"
       name: kibana 
     - src: git@github.com:
       scm: git
       version: "2.1.4"
       name: filebeat 

```
12. Переработайте playbook на использование roles.
В данном случае под playbook понимается файл `site.yml` в директории `/ansible/playbook/site.yml`

* Команда запуска плейбука в данной конфигурации: `ansible-playbook -i inventory.host.yml site.yml`
```yml
- name: Assert elasticrole
  hosts: all
  roles:
    - elastic
    - kibana
    - filebeat
tags: [0.1.0]
```
13. Выложите playbook в репозиторий.

* Quick setup — if you’ve done this kind of thing before
```ps
https://github.com/zakharovnpa/kibana_roles.git
```

```ps
git@github.com:zakharovnpa/kibana_roles.git
```

* …or create a new repository on the command line
```ps
echo "# kibana_roles" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/zakharovnpa/kibana_roles.git
git push -u origin main

```
* …or push an existing repository from the command line
```ps
git remote add origin https://github.com/zakharovnpa/kibana_roles.git
git branch -M main
git push -u origin main
```
* Добавление тэгов коммитам
```ps
git tag -a v0.0.01 -m "init version"
```
```ps
git push origin --tags
```

14. В ответ приведите ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

* Запуск `ansible-playbook -i inventory/hosts.yml site.yml` и листинг работоспособного плейбука, запускающего 
```ps
root@PC-Ubuntu:~/ansible-learning/yandex-cloud/Eta/ansible/playbook# ansible-playbook -i inventory/hosts.yml site.yml 
[DEPRECATION WARNING]: "include" is deprecated, use include_tasks/import_tasks instead. This feature will be removed in version 2.16. Deprecation warnings can be disabled by setting 
deprecation_warnings=False in ansible.cfg.

PLAY [el-instance] *******************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [el-instance]

TASK [elasticsearch_roles : Download Elasticsearch's rpm] ****************************************************************************************************************************************************
changed: [el-instance]

TASK [elasticsearch_roles : Install Elasticsearch] ***********************************************************************************************************************************************************
changed: [el-instance]

TASK [elasticsearch_roles : Configure Elasticsearch] *********************************************************************************************************************************************************
changed: [el-instance]

RUNNING HANDLER [elasticsearch_roles : restart Elasticsearch] ************************************************************************************************************************************************
changed: [el-instance]

PLAY [k-instance] ********************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [k-instance]

TASK [kibana_roles : include_tasks] **************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/kibana_roles/tasks/download_kibana_rpm.yml for k-instance

TASK [kibana_roles : Download Kibana's rpm] ******************************************************************************************************************************************************************
changed: [k-instance]

TASK [kibana_roles : include_tasks] **************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/kibana_roles/tasks/install_kibana.yml for k-instance

TASK [kibana_roles : Install Kibana] *************************************************************************************************************************************************************************
changed: [k-instance]

TASK [kibana_roles : include_tasks] **************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/kibana_roles/tasks/configure_kibana.yml for k-instance

TASK [kibana_roles : Configure Kibana] ***********************************************************************************************************************************************************************
changed: [k-instance]

RUNNING HANDLER [kibana_roles : restart kibana] **************************************************************************************************************************************************************
changed: [k-instance]

PLAY [application-instance] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [application-instance]

TASK [filebeat_roles : include_tasks] ************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/filebeat_roles/tasks/download_filebeat_rpm.yml for application-instance

TASK [filebeat_roles : Download Filebeat's rpm] **************************************************************************************************************************************************************
changed: [application-instance]

TASK [filebeat_roles : include_tasks] ************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/filebeat_roles/tasks/install_filebeat.yml for application-instance

TASK [filebeat_roles : Install Filebeat] *********************************************************************************************************************************************************************
changed: [application-instance]

TASK [filebeat_roles : Configure Filebeat] *******************************************************************************************************************************************************************
changed: [application-instance]

TASK [filebeat_roles : include_tasks] ************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/filebeat_roles/tasks/set_filebeat_systemwork.yml for application-instance

TASK [filebeat_roles : Set filebeat systemwork] **************************************************************************************************************************************************************
changed: [application-instance]

TASK [filebeat_roles : include_tasks] ************************************************************************************************************************************************************************
included: /root/ansible-learning/yandex-cloud/Eta/ansible/playbook/roles/filebeat_roles/tasks/load_kibana_dashboard.yml for application-instance

TASK [filebeat_roles : Load Kibana dashboard] ****************************************************************************************************************************************************************
ok: [application-instance]

RUNNING HANDLER [filebeat_roles : restart filebeat] **********************************************************************************************************************************************************
changed: [application-instance]

PLAY RECAP ***************************************************************************************************************************************************************************************************
application-instance       : ok=11   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
el-instance                : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
k-instance                 : ok=8    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

## Необязательная часть

1. Проделайте схожие манипуляции для создания роли logstash.

```ps

```
2. Создайте дополнительный набор tasks, который позволяет обновлять стек ELK.

```ps

```
3. Убедитесь в работоспособности своего стека: установите logstash на свой хост с elasticsearch, настройте конфиги logstash и filebeat так, чтобы они взаимодействовали друг с другом и elasticsearch корректно.

```ps

```
4. Выложите logstash-role в репозиторий. В ответ приведите ссылку.

```ps

```

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
