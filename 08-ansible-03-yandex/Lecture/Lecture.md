## Лекция по теме "08.03 Использование Yandex Cloud"

Использование Yandex Cloud
Алексей
Метляков

### 1Алексей Метляков
DevOps Engineer
OpenWay
Алексей Метляков

### 2План занятия
1. Что такое Yandex.Cloud?
2. Способы создания VM
3. Статический Inventory
4. Динамический Inventory
5. Использование Ansible
6. Итоги
7. Домашнее задание

### 3Что такое Yandex.Cloud?

### 4Что такое Yandex.Cloud?
Yandex.Cloud – облачная платформа от Яндекса, позволяющая поднять нужное количество ресурсов и настроить их по своему
усмотрению
Иными словами, Yandex.Cloud позволяет:
* Создавать шаблонные ресурсы (для БД, registry, DNS и т.д.);
* Создавать простые VM для последующей гибкой настройки на
ваше усмотрение;
* Создавать инфраструктуру «на лету»;
* Платить только за используемое время.
Официальная страница Yandex.Cloud

### 5Способы создания VM
### 6Способы создания VM
VM можно создавать при помощи:
* web-интерфейс
* yandex cli
* terraform
* ansible

Во всех случаях, кроме использования ansible-plugin, для
управления хостами через ansible необходимо описать их в
` inventory `.

### 7Статический Inventory
### 8Статический Inventory
Вид inventory, который мы чаще всего видим, по сути файл
(yaml или ini), в котором перечислены ` hosts `, ` groups ` и
дополнительные параметры.

```yml
---
prod: # Группа серверов
  children:
    nginx:
      hosts:
        prod-ff-74669-02:
          ansible_host: 255.245.12.32
          ansible_user: prod
  children:
    application:
      hosts:
        174.96.45.23:
test:
  children:
    nginx:
      hosts:
        localhost:
          ansible_connection: local
```
### 9Cтруктура директории с Playbook
```
group_vars/
          all/
              some_variable.yml
          <group_name>/

inventory/
    prod.yml
    test.yml

roles/
     <role_fodlers>/

site.yml
requirement.yml
```

### 10Cтруктура директории с Playbook
```
inventory/
         prod/
         group_vars/
                   all/
                       some_variable.yml
         <group_name>/
                      hosts.yml
roles/
     <role_fodlers>/
     
site.yml
requirement.yml
```

### 11Динамический Inventory
### 12Динамический Inventory
Так как ansible, в момент исполнения содержит все данные в json формате, а inventory для него это всегда перечисление hosts в
groups с их возможными параметрами:
* Для построения динамического inventory должен существовать некий скрипт, который сможет передавать json на выход с
описанием hosts в облаке;
* Если существует модуль для создания hosts в облаке – он должен уметь собирать динамическое inventory в процессе
создания hosts.

### 13Использование Ansible
#### 14Модули для создания инстансов
Ansible имеет набор модулей для создания инстансов:

* AWS
* OpenStack
* k8s
* docker
* podman
* Google Cloud
* Microsoft Azure
* Vultr
Полный перечень модулей можно посмотреть на официальной
странице.

#### 15Модули для управления inventory
Ansible имеет набор модулей для создания inventory:

* AWS
* OpenStack
* k8s
* docker
* Google Cloud
* Microsoft Azure
* Vultr
Полный перечень модулей можно посмотреть на официальной
странице.

#### 16Как написать Playbook?
Изначально нужно ответить на два вопроса:
* Для чего нам нужен playbook?
* На какие подзадачи можно разделить цель?
Далее нужно работать над содержанием playbook:

* Приготовить структуру директорий;
* Приготовить стартовые файлы;
* Организовать inventory для тестовых прогонов;
* Описать структуру plays и tasks внутри;
* При необходимости – параметризировать все tasks при помощи ` vars `.

#### 17Схема будущего решения
```
el-instance
k-instance
Elasticsearch
Kibana
application-instance
filebeat
logs
```

#### 18Схема целевого решения
```
el-instance
Elasticsearch
k-instance
Kibana
Logstash
app2-instance app-instance
filebeat filebeat
logs logs
```

#### 19Схема будущего решения
```
el-instance
k-instance
Elasticsearch
Kibana
application-instance
filebeat
logs
```

20Как запустить Playbook?
* Первый запуск стоит осуществлять или на тестовом окружении
или с флагом: 

` ansible-playbook -i inventory/<inv_file>.yml <playbook_name>.yml --check `
* Если были найдены ошибки: 

` ansible-playbook -i inventory/<inv_file>.yml <playbook_name>.yml --start-at-task <task_name> `
* Для запуска исполнения в полуинтерактивном виде:

` ansible-playbook -i inventory/<inv_file>.yml <playbook_name>.yml --step `
* Полноценный запуск playbook в целевом виде должен выглядеть: 

` ansible-playbook -i inventory/<inv_file>.yml <playbook_name>.yml `

### 21Итоги
### 22Итоги
* Существует несколько видов inventory: динамический и
статический;
* Статический используется чаще всего и полностью описывает
существующие хосты;
* Динамический нужен для создания и опроса динамической
конфигурации ресурсов;
* Динамические inventory создаются при помощи специальных
plugins;
* Хосты для динамического inventory можно создавать при помощи
modules;
* Inventory без group_vars и host_vars не имеет никакой ценности
 Статические inventory можно описывать двумя видами;
* Весь процесс создания playbook – описание host, их параметров в
vars и перечислении действий (task), которые необходимо
выполнить для получения желаемого результата.

### 23Домашнее задание
Давайте посмотрим ваше домашнее задание.
* Вопросы по домашней работе задавайте в чате мессенджера
Slack.
* Задачи можно сдавать по частям.
* Зачёт по домашней работе проставляется после того, как приняты
все задачи.

### 24Задавайте вопросы и
пишите отзыв о лекции!
Алексей Метляков
Алексей Метляков