## Лекция по теме "08.05 Тестирование Roles"


### Тестирование Roles    00:09:00
Алексей
Метляков
1Алексей Метляков
DevOps Engineer
OpenWay
Алексей Метляков

### 2План занятия
1. Как тестировать Role?
2. Molecule
3. Tox
4. Итоги
5. Домашнее задание

### 3Как тестировать Role?    00:09:47
В данном виде тестирование Role превращается в достаточно
сложную задачу, так как нужно:
- Провести проверку синтаксиса;
- Подготовить тестовое окружение;
- Провести проверку на работоспособность;
- Провести проверку на идемпотентность;
- Исправить ошибки на каждом из этих этапов и повторять весь
сценарий, пока не будет получен положительный результат.

### 4Molecule   00:12:39
Данный фреймворк позволяет избавить нас от рутины и заниматься
только созданием и исправлением ошибок. Он умеет:
- Создавать новые roles;
- Создает директории для новой роли
- Создает директории в существующих ролях (т.н. сценарии дестирования - scenarios)
- Создавать scenarios тестирования;
- Тестировать roles против разного окружения;
- Поддерживает docker, podman, delegated как драйверы подключений.
Официальная документация [тут](https://molecule.readthedocs.io/en/stable-1.18/index.html#)
- Молекула может сама создавать различную инфраструктуру. Поддерживает docker, podman, delegated, vagrant, vmware как драйверы подключений. delegated - универсальное

### 5Как создать Role?      00:16:05
Если у нас нет никакой роли
- Создать стандартную структуру директорий и файлов при
помощи команды `molecule init role <name_role>;
- 00:17:25 - появились новые директории
- Создать необходимые tasks, handlers;
- Определить все необходимые переменные в defaults и vars;
- Создать готовый тестовый playbook в tests, заполнить файлы molecule для проведения тестирования;
- Заполнить meta всю информацию о роли, наиболее полно описать её в README.md.

### 6Структура директорий   00:16:59
После инициализации новой роли мы получаем следующие
директории и файлы:
- стандартный набор директорий и файлов для role;
- `molecule/<name_scenario>` – набор **scenarios** для тестирования.
Внутри любого **scenario** находятся следующие файлы:
- `molecule.yml` – основной файл для molecule;
- `converge.yml` – playbook, который molecule будет использовать для запуска тестов;
- `verify.yml` – дополнительные тесты после исполнения role.


- 00:17:20 - Команда запуска по созданию новой роли молекулой `molecule init role new_role`. Появится новая директория `new_role`
- 00:18:20 - Помимо всего появилась директория `/molecule/default`. `default` - это имя сценария тестирования.
- 00:18:50 - описание файлов в сценарии тестирования
- 00:19:20 - описание `molecule.yml`. Это самый основной файл молекулы

* `molecule.yml`
  ```yml
  ---
   dependency:
     name: galaxy
     
     # Здесь можно добавить requirements для скачивания ролей, которые необходммо тестировать - 00:19:50
     # В этом сценарии создать файл requirements.yml и когдазапустится тестирование, молекула найде этот файл 
     # и скачает в свой кэш эти роли прогнать перед тем, как прогонять основную роль
   
   
   driver: 
      name: delegated     # это драйвер, с помощью которого будет проходть тестрование. по умолчанию delegated
    # name: docker
    # name: podman
    # name: openstack     
   
   
   platforms:     # перечисление ВМ, которые будут создаваться этим драйвером
     name:
       - el-instane
       - k-instane
       - applications-instane  # если драйвер мспотзуется Docker, то эта секця напоминает Docker-Compuse yml manifest
                                # Здесь можно перечслять Images, Volume, Group и прочее
                                   
      
   provisioner:                 # кто запускает против тех платформ, которые описаны
     name: ansible
       inventory:               # сюда же можно разместить group_vars, host_vars
         group_vars:
           group_name:
             name_variables
             
             
   verifier:             # -00:22:30 Для того, чтобы быть уверенным, что те змененя, которые мы хотел получить, мы точно получили
     ansible              # Ранее это была `testinfra`
                          #  На самом деле мы пишем некоторый плейбук в файл `verify.yml`в котором опсываем assert
     
   ```  
  
- 00:20:00 - Внутри директории `molecule` можно создать `requrements.yml`, на основани икоторого все будет скачаны нужные роли
- 00:22:20 - про `verifier`. Это для того, чтобы после прогонки всего тестирования проверить, что действительно, те изменения, которые мы хотели получить на ВМ мы получили. Ранее это была `testinfra`

* На самом деле мы пишем некоторый плейбук в файл `verify.yml`
  ```yml
  ---
  - name: Verify
    hosts: all
    gathet_facts: false
    tasks:                        # здесь мы описываем assert на то, чтбобы проверть, что тут-то файлк создан, и тут создан
    - name: Example assertion     # что все резолвится с такой-то версиией команда на проверку версии
      assert: 
        thet: true
  
  ```
- 00:23:20 - `verify.yml` отрабатывается в самом конце отработки `molecule.yml`. Молекуле все равно что запускать. Хоть здесь еще одну роль запиши и она ее запустит. Но это наша возможность прверить, что все хорошо прошло.
- 00:24:10 - в директории `molecule` также лежат файлы `convrege.yml` пример файла `create.yml` - для создания ВМ при помощи какого-то драйвера, `destroy.yml` - для удаления ВМ. Create и Destroy при драйвере Docker не появляются. 
- 00:24:40 - при драйвере Docker ВМ создаются,  дет тестирование
- 00:24:50 - при создан ВМ в облаках, то файл `create.yml` - это просто плейбук

* Файл `create.yml` с драйвером Delegated нацелен на то, чтобы создавать днамческое инвентори. Это только для того, чтобы получить дампы с конфгами нстансов. Получает данные из ``with.items: "{{ server.results }}"``
```yml
---
- name: Create
  hosts: localhost
  connection: local
  gather_facts: false
  no_lod: "{{ molecule_no_log }}"
  tasks:
  
  # TODO: Developer must implement and populate `server` variable
  
    - when: server.changed | default(false) | bool ``# надо чтобы в переменной server попал все сервера, которые мы создавал
    block:
      - name: Populate instanse config dict
        set_facts:
          instanse_config_dict: {йлы
            'instance': "{{  }}",
            'address': "{{  }}",
            'user': "{{  }}",
            'port': "{{  }}",
            'identity_file': "{{  }}", }
        with_items: "{{ server.results }}"        # для того, чтобы получить дампы с конфгами нстансов - 00:25:25
        register: instance_config_dict
      - name: Convert instance config dict to a list
        set_fact:
          instance_conf: "{{ instance_config_dist.results | map(attribute....) }}"
      - name: Dump instance config
        copy:
          content: | 
             # Molecule managed
             {{ instance_conf | to_json | from_json | to_yaml }}
          dest: "{{ molecule_instance_config }}"
          mode: 0600 
```
- 00:24:23 - Файл `destroy.yml`
```yml
- name: Destroy
  hosts: localhost
  connection: local
  gather_facts: false
  no_lod: "{{ molecule_no_log }}"
  tasks:
  - name: Populate instanse config
    set_facts:
          instanse_config_dict: {}
  - name: Dump instance config
    copy:
      content: | 
        # Molecule managed
        {{ instance_conf | to_json | from_json | to_yaml }}
        dest: "{{ molecule_instance_config }}"
        mode: 0600 
    when: server.changed | default(false) | bool
  
```
- 00:24:30 - `create.yml` и `destroy.yml` появляются только тогда, когда используется либо OpenStack дрейвер,либо Delegate драйвер
- 00:24:40 - `create.yml` когда в Docker работает, таб плейбук гонять не надо, а когда ВМ в облаках, то это плейбук - это несколько тасок, которые создают то самое динамическое инвентори.
- 00:25^50 - надо в переменную `server` чтобы попали все наши создаваемые сервера. А молекула сама заберет оттуда все что ей нужно  будет использовать
- 00:26:00 - про `destroy.yml`Это для унчтожения ВМ. начала создаются таски для того, чтобы унчтожить ВМ,  а потом молекула опустошает те дампы  те конфги, которые она создала в момент Create
- 00:26:20 -`converge.yml`- это просто плейбук, чтобы запустить нашу роль

* `converge.yml`
```yml
---
- name: Converge
  hosts: all
  become: true  # это не самая хорошая практика, но можно, есл очень надо
  tasks:
  - name: "Include new role"
    inclede_role:
    name: "new_role"
```

- 00:27:00 - если мы создадим сценарий не с дефолотовым драйвером Delegate, а с драйвером Docker, то мы можем дополнтельный сценарий тестрованя накрутить 
- 00:27:30 - !!! Зайти в директорию с ролью и запустить команду `molecule init scenario <name_scenery docker>  --driver-name docker`


- 00:29:15 - !!! установка пакета `molecule_docker` `pip3 install "molecule_docker<0.3"`
- запуск команды `molecule init scenario <name_scenery docker>  --driver-name docker`
- 00:28:40 - что-то там не понравилось и решили запускать на openstack


- запуск команды `molecule init scenario open --driver openstack`
- 00:29:05 - появилась новая директория `open`. Это наш новый отдельный сценарий
- 00:29:35 - описание файла сценария `molecule.yml`
- 00:29:57 - описание файла сценария `create.yml`. Его мы почти никогда не меняем, кроме случая с драйвером Delegated
- 00:31:00 - описание файла сценария `destroy.yml`
- 00:31:05 - !!! описание файла сценария `prepare.yml` Для подготовки хоста, чтобы на нем работала роль или молекула. Он сначала создал ВМ, потом запускает шаг prepare и проверяет, например, установлен ли в ОС python. Если нет, то он устанавливается. Содержимое можно переписать на свое.

* prepare.yml
```yml
---
- name: prepare
  hosts: all
  gather_facts: false
  tasks:
    - name: Install Python for Ansile
      raw: test -e /usr/bin/python || (apt -y update && apt install -y python-minimal python-zipstream)
      become<; true
      changed_when" false
 ```


- 00:32:00 - !!! Если мы говорим о сценариях для  Докера, то они выглядят намного проще
 
* Файл `default/molecule.yml` 
  ```yml
  ---
   dependency:
     name: galaxy
   driver: 
     name: docker #по умолчанию delegated
   platforms:     # перечисление ВМ, которые будут создаваться этим драйвером
   - name: Centos7
     image: docker.io/pycontris/centos:7
     pre_build_image: true  # значит, что образ готов, его не надо дополнительно собрать и конфгурровать. 
                           # Molecule скачвает образ. Бери его и используй с именем Centos7
                           
                           # Но если бы тут было false,то можно в эту же директорию положить Dockerfile. 
                            # Molecule автоматически этот образ бы собирала
                            # и использовала  - 00:32:25
                            
   - name: Ubuntu
      image: docker.io/pycontris/ubuntu:latest
      pre_build_image: true
      
   provisioner:   # кто запускает против тех платформ, которые описаны
     name: ansible
     # сюда же можно разместить group_vars, host_vars
       inventory:
         group_vars:
           group_name:
             name_variables
   verifier:
     ansible
     
   lint: |             # сюда можно добавить линтер. Статический анализ. 
                       # Здесь прописан bash-script, который построчно вызывает ansible-lint и потом yamllint
                       # скрипты должны лежать здесь: etc/bash_completion.d.
     ansible-lint .
     yamllint .
   
   scenario:            # сюда можно добавить какие scenario будут запускаться. Определение последовательности выполнения шагов внутри сценария
     - default
     - <name_scenery>
   ```
- 00:32:30 - !!! о том, как включить работу молекулы с файлом Dockerfile. `create` `destroy` тут никакого нет, потому что по факту вместо файла  `create`  - это команда `docker run -d sleep 6000000`, а  в место`destroy` - команда `docker stop`, `docker rm`  и все, до свидания
- 00:33:27 - о папке `/alternative` все тоже самое, только для `podman`, а это тот же Docker, только сбоку.
- 00:33:45 - подытог. 

После инициализации новой роли мы получаем следующие
директории и файлы:
- стандартный набор директорий и файлов для role;
- `molecule/<name_scenario>` – набор **scenarios** для тестирования.
Внутри любого **scenario** находятся следующие файлы:
- `molecule.yml` – основной файл для molecule;
- `converge.yml` – playbook, который molecule будет использовать для запуска тестов;
- `verify.yml` – дополнительные тесты после исполнения role.

### 7Структура molecule.yml   00:34:23
Внутри файла находятся следующие директивы:
- dependency – перечисление зависимостей роли. Чем скачиваются, например **galaxy**;  
- driver – указание параметров выбранного driver, напримерб **docker;
- platform – перечисление хостов для выбранного driver;
- provisioner – указание поставщика для molecule, например,**ansible**; там же можно указать **group_vars**
- veriﬁer – выбор framework для проведения проверок.
Туда же можно добавить:
- ключ lint – конфигурирование синтаксческого анализа linter для тестирования;
- ключ scenario – для переопределения последовательности выполнения шагов внутри сценариев тестирования.

Об этом смотреть на  00:32:00 - о сценариях для  Докера
* !!! Повтор
  ```yml
  ---
   dependency:
     name: galaxy
   driver: 
     name: docker #по умолчанию delegated
   platforms:     # перечисление ВМ, которые будут создаваться этим драйвером
   - name: Centos7
     image: docker.io/pycontris/centos:7
     pre_build_image: true  # значит, что образ готов, бери его и используй с именем Centos7
                            # если бы тут было false,то сожно в эту же директорию положить Dockerfile. Molecole автоматически этот образ бы собирала
                            # и использовала
                            
   - name: Ubuntu
      image: docker.io/pycontris/ubuntu:latest
      pre_build_image: true
      
   provisioner:   # кто запускает против тех платформ, которые описаны
     name: ansible
     # сюда же можно разместить group_vars, host_vars
       inventory:
         group_vars:
           group_name:
             name_variables
   verifier:
     ansible
     
   lint: |             # сюда можно добавить линтер. Статический анализ. Здесь прописан bash-script, который построчно вызывает ansible-lint и потом yamllint
                       # скрипты должны лежать здесь: etc/bash_completion.d.
     ansible-lint .
     yamllint .
   
   scenario:            # сюда можно добавить какие scenario будут запускаться. Определение последовательности выполнения шагов внутри сценария
     - default
     - <name_scenery>
   ```


### 8Как правильно тестировать Role?   00:35:04
В данном виде тестирование role упрощается, так как нужно:
- Подготовить тестовое окружение;
- Запустить сценарий проверки через `molecule test`;
- Исправить ошибки на каждом из этих этапов и повторять весь
сценарий, пока не будет получен положительный результат.

- 00:35:40 - И тогда думать об инфраструктуре уже не надо. Она вся описана в коде

### 9Как правильно тестировать Role?   00:35:50
Тестирование условно можно разделить на три вида:
- Использование полного сценария тестирования;
- Использование собственных сценариев тестирования;
- Использование отдельных частей сценария самостоятельно.

### 10Как правильно тестировать Role?   00:36:00
Команда `molecile test` запускает полный сценарий тестирования role.
Полный сценарий включает в себя:
- запуск команды `molecile test`

Затем молекула пытается выполнить:
- lint – прогон линтеров; При наличии соответствующего ключа в плейбуке
- destroy – удалить старые инстансы с прошлого запуска; для Docker просто пытается удалть контейнер
- dependency – производит установку при помощи `ansible-galaxy` тех ролей, которые опсаны в `requirements.yml` ansible-зависимостей, если есть;
- syntax – !!! проверка синтаксиса с помощью `ansible-playbook --syntax-check`; Это в данном случае делает сама молекула проверяет синтаксис файла плейбука `converge.yml`. 
- create – создание инстансов для тестирования;
- prepare – подготовка инстансов, если это необходимо.

### 11Как правильно тестировать Role?        00:37:30
`molecule test` запускает полный сценарий тестирования role.
Полный сценарий включает в себя этапы с поочередным запуском файлов:
- converge – запуск тестируемого плейбука `converge.yml`;
- idempotence – проверка на идемпотентность при помощи повторного запуска  плейбука `converge.yml`; Есл хотя бы одна таска выйдет с результатом `change`, то все, идемпотентност нет
- side_effects – действия, которые не относятся к role, но необходимые для тестирования;
- 00:38:00 - для этого надо написать отдельный файл `side_effects.yml` и в нем описать какие-то действия, которые будут после того, как мы прогнали роль, но до того, как мы хотим протестировать при помощи `verify.yml`

- verify – запуск тестов с помощью указанного фреймворка тестирования;
- cleanup – очистка внешней инфраструктуры от результатов тестирования; это отдельный плейбук `cleanup.yml` он лежит рядом со сценарием тестирования. Он нужен для того, чтобы в случае, если мы работаем на инфраструктуре, которую не создаем динамически. Например можно подключаться по ssh через Molecule, но то все достаточно сложно вываливается (через Vagrant кто-там откручивал каким-то образом) - 00:39:10
В общем, для того, чтобы очстить наши ВМ от того, что мы наделали при помощ роли, можно написать в отдельном плейбуке `cleanup.yml`
В этом случае никакие Destroy, Create не будут проходить. Этх файлов просто не будет.
-00:38:45 - второй вариант, когда нам нужен Cleanup, это когда, даже если у нас есть днамческое инвентори, но также есть и статческий нвентори, где тоже надо удалть все то, что сделал роли. Это могут быть лишние дитсрибутивы на локалхосте, или на каких угодно серверах
- destroy – файл плейбук `destroy.yml` уничтожение инстансов, созданных ранее для выполнения задач тестирования.

### 12Как правильно тестировать Role?        00:40:35 и    00:42:25
- Каждую из указанных частей сценария можно вызвать
отдельно, но нужно держать в уме, что у каждой из них могут
быть зависимости.
- Для того чтобы понимать, что каждая из tasks будет запускать, в
случае отдельного вызова – необходимо пользоваться
конструкцией `molecule matrix <task_name>`.
- Перед тем, как уничтожать инстансы, к ним можно подключиться
и в ручном режиме проверить все изменения в системе.
- Оставить возможность подключения можно и с полным
сценарием тестирования, воспользовавшись параметром
`--destroy=never`.
-  00:42:40 - !!! когда мы запускаем  не `molecule test`, а `molecule converge`, то запускается он не сразу, а сначала запустятся те таски, от которых этот `converge` зависит.
-  00:40:40 - !!! Для того чтобы понимать, что каждая из tasks будет запускать, в случае отдельного вызова – необходимо пользоваться 
конструкцией `molecule matrix <task_name>`.

- 00:43:10 - Пример: 
`molecule-matrix converge` -       выдаст список **scenery** с шагами в каждом из них.

```
INFO    Tets matrix

default:
  - dependency
  - create
  - prepare
  - converge
open:
  - dependency
  - create
  - prepare
  - converge
```

`molecule-matrix converge -s open`  -выдаст только для отдельного сцеария **open** Ключ `-s` - значит **scenario**

```
open:
  - dependency
  - create
  - prepare
  - converge
```

`molecule converge` - не удаляет за собой инстансы. Он прогонит роль, и можно к этим ВМ подключиться и посмотреть что там. 
- 00:44:30 - Причем подключиться можно тоже через молекулу. `molecule login -s <scenery_name> --host <hostname>` И тогда молекула автоматически подключится к хосту (хоть Докер, хоть к любому) с тем кредами, с которыми был хосты созданы, под тем пользователем, который был указан в нвентори подключается и там можно удаленно работать.
- 00:45:10 - `molecule converge--destroy=never` - не удалять ВМ после тестирования. Оставить возможность подключения можно и с полным сценарием тестирования, воспользовавшись параметром `--destroy=never`

### 13Как правильно тестировать Role?     00:45:20
Очерёдность сценариев можно переопределить через
директиву `scenario`. 
Формат записи в `molecule.yml` будет выглядеть так:

```yml
---
scenario:
  <task>_sequence:
    - list
    - of
    - tasks
```

```yml
---
#Example of redefined of test scenario
scenario:
  test_sequence:
    - create
    - converge
    - idempotence
    - destroy
```

- 00:45:40 - Команда   `molecule matrix test -s open` покажет стадии, которые далеко не все описаны в соответствующих файлах.
```
open:
  - dependency
  - lint
  - cleanup
  - destroy
  - syntax
  - create
  - prepare
  - converge
  - idempotence
  - side_effect
  - verify
  - cleanup
  - destroy

```


- 00:46:00 - пояснение по файлу    `new_role/molecule/open/molecule.yml`. Как задать очередность выполнения **scenery**
!!! Если мы на тесте делать не все из этого, то мы можем это все переопределить, то впсываем в файл секцию `scenario`

```
# что-там в начале файла

scenery:
  test_sequence:
    - desctroy
    - create
    - converge
    - verify
    - destroy

```
- 00:46:35 - для проверки запускаем `molecule matrix test -s open`. Здесь test - имя сценария тестрования, open - имя роли.

```
INFO    Test matrix

open:
  - desctroy
  - create
  - converge
  - verify
  - destroy

```
- 00:46:50 - вывод команды `molecule matrix test` покажет, что порядок стадий переопределился

```
INFO    Test matrix

default:
  - dependency
  - lint
  - cleanup
  - destroy
  - syntax
  - create
  - prepare
  - converge
  - idempotence
  - side_effect
  - verify
  - cleanup
  - destroy

open:
  - desctroy
  - create
  - converge
  - verify
  - destroy

```

### 14 !!! Список основных команд molecule     00:47:25
```
molecule init role --driver-name <driver> <rolename>
```
```
molecule init scenario --driver-name <driver> <scenarioname>
```
```
molecule test
```
```
molecule test --destroy=never
```
```
molecule matrix <taskname>
```
```
molecule matrix -s <scenarioname> <taskname>
```
```
molecule <taskname>
```


- 00:48:00 - !!! Работа молекулы с облаками. Показ эксклюзива. Описание файла сценария `default/molecule.yml` для того, чтобы два сервера Вектор могли друг другу передавать данные

```yml
---
dependency:
  name: galaxy
lint: |           # здесь прописан bash-скрипт, который построчно вызывает ansible-lint против всех файлов, а потом yaml-lint
  ansible-lint .
  yaml-lint .
driver:
  name: openstack
platforms: 
- name: ${USER}--${BUILD_NUMBER:-locaL}-molecule-vector-role-centos7-outcomer
        # такое имя нужно для того, чтобы CI, когда эта штука запускается на TeamCity чтобы она могла работать в параллель. 
        # Т.е. создается уникальное и индивидуальное имя ВМ, где:
        # ${USER} - имя пользователя в ОС
        # ${BUILD_NUMBER:-local} - номер сборки в TeamCity. Если его нет, то пишется -locaL
        # Когда будет запускаться ВМ, то будет создаваться она с именем на основании шаблона
  groups:         # группа для объединения создаваемых ВМ в одну группу ansible для прописывания group_vars
    - vector_inner

  image: centos-7-x86_64
  flavor: 1.2.60
  network: external
  ssh_user: ${MOLECULE_OPENSTACK_SSH_USER:-cloud-user}
  
          # ${MOLECULE_OPENSTACK_SSH_USER:-cloud-user} - имя пользователя. Если его нет, то пишется -cloud-user
          # Когда будет запускаться ВМ, то будет запускаться она от имени на основании шаблона
  

- name: ${USER}--${BUILD_NUMBER:-locaL}-molecule-vector-role-centos7-incomer
  image: centos-7-x86_64
  flavor: 1.2.60    # 1 - кол-во CPU, 2 - размер оперативки 2Gb, 60 - HDD 60Gb
  network: external
  ssh_user: ${MOLECULE_OPENSTACK_SSH_USER:-cloud-user}
  groups:             # группа для объединения создаваемых ВМ в одну группу ansible для прописывания group_vars
    - vector_outer
    
    
  provisioner:
    name: ansible
    inventory:
      group_vars:
   #  host_vars:    # можно наркутить переменных по хосту
                    # Далее идут настройки для того, чтобы два сервера Вектор могли между собой общаться. Чтобы один другому отправлял какие-то данные
                    # В целом это файлы yaml
                    # !!! Алексей сказал, что они нам не очень сильно нужны.
                   
        vector_inner:
          vector_config:
            sources:
              local:
                type: socket
                address: 127.0.0.1:6767
                max_lenght: 102400
                mode: "tcp"
            transforms:
              add_hostname:
                type: add_fields
                inputs: ["local"]
                overwrite: true
                fields:
                  host: "{{inventory_hostname}}"
            sinks: 
              to file:
                type: file
                inputs: "add_hostname"
                healthcheck: true
                path: "{{ vector_confnig_dir}}/local.log"
                encoding:
                  codec: ndjson
              to_clickhouse_vector:
                type: file
                inputs: "add_hostname"
                address: "{{ hostvars[groups['vector_outer'] [0]]['ansible_host']}}:6767
                healthcheck: 
                  enable: false
                buffer:
                  max_events: 500
                  type: memory
                  when_full: block
        vector_outer:
          vector_config:
            sources:
              local_logs:
                type: vector
                address: 0.0.0.0:6767
            sinks:
              to_file:
                type: file
                inputs: ["local_logs"]
                path: "{{ vector_config_dir }}/outer.log"
                encoding:
                  codec: ndjson

     all:
       vector_version: 0.18.1
       vector_packages_url: https://packages.timber.io/vector/
       force_reinsrall: false
       vector_distr_is_local: false
       need_create_service: true
       vector_config_dir: "/home/cloud-user/vector-config"
       
verifier:
  name: ansible
          
    - 00:52:45 показано продолжение файла
                  
                  
                  
                  
        # To be Continue
                  
    

  
  
```

- 00:52:50 - `create.yml` - не изменялся, он абсолютно идентичен тому, что создала сама молекула

- 00:52:56 - Описание файла `destroy.yml`
* Было добавлено для удаления key_pair на openstack сервере:
```
- name: Delete key pair
  os_keypair:
    name: "key-pair-{{ molecule_yml['platforms'][0]['name'] }}"
    state: absent
```

- 00:53:29 - Описание файла `verify.yml` Тут есть дополнительные проверки того, что оба сервер могут между собой передавать сообщения
```yml
# verify.yml`
#Первый Play для локального вектора
- name: Check local vector
  hosts: vector_inner
  gather_facts: false
  tasks:
  - name: Install nc    № Устанавливаем Netcast (или NetCad?) для отправки первоначальному вектору по его порту 6767 какие-то данные
    become: true
    yum:
      name: nc
      state: present
   - name: Run debug message    # отправление тестового сообщения "debug_mesage" 
     shell: set -o pipefail && echo "debug_mesage" | nc 127.0.0.1:6767 # здесь задаем передачу данных к локалхостному Вектору, и чтобы тот тоже смог их 
                                                                       # перебросить.
     register: qwe
     changed_when: false
   - name: Check local vector work correctly   # Проверяем, что тестовое сообщение записалось в local.log припомощи slurp
     slurp:
       src: "{{ vector_config_dir }}/local.log"
       register: mounts
   - name: Set correct facts        # декодирование и создание из mounts в файл "debug_msg.message"
       set_fact:
         debug_msg: "{{ mounts['content'] | b64decode }}"
   - name: Ensure message in sent  # проверяется, что слова "debug message" появились в файле "debug_msg.message"
     assert:    # !!! Пример asserta
       that: "debug message" in "debug_msg.message"
       
# - 00:54:44
#Второй Play для удаленного вектора
- name: Check outer vector   
  hosts: vector_outer
  gather_facts: false
  tasks:
  - name: Check    # Проверяем, что тестовое сообщение записалось в local.log припомощи slurp
    slurp:
      src: "{{ vector_config_dir }}/local.log"
      register: mounts
  - name: Set correct facts        # декодирование и создание из mounts в файл "debug_msg.message"
      set_fact:
        debug_msg: "{{ mounts['content'] | b64decode }}"
  - name: Ensure message in sent  # проверяется, что слова "debug message" появились в файле "debug_msg.message"
    assert:
      that: "debug message" in "debug_msg.message"
```
- 00:55:00 - о том, что успешное выполнение прверок по вышеизложенному файлу `verify.yml` подтверждает, что собранные по этой роли сервера работоспособны. Очень незатейливый сценарий.


- 00:55:27 - !!! Важно! Запуск команды `molecule test` и пояснение что происходит
- 00:55:45 - показаны стадии тестировани
``
INFO    Default scenario test matrix: dependency, lint, cleanup, 
``
- 00:55:50 - установка ролей
- 00:56:33 - запуск Destroy
- 00:57:14 -  создание инстансов
  -  запуск и прохождение тестиования
  -  удаление инстансов
- Мы один раз все настроили в `default/molecule.yml` 

- 00:59:50 - !!! теперь мы можем в файлах директории `role/tasks` все что угодно понаписать и пытаться все это дело прогнать.
* Пример файлов с тасками
```ps
#/role/tasks/
            config_vector.yml
            install_vector_apt.yml
            install_vector_dnf.yml
            install_vector_yum.yml
            main.yml
            manage_vector.yml
            precheck_vector.yml
            remove_vector_apt.yml
            remove_vector_dnf.yml
            remove_vector_yum.yml
            service_vector.yml
            
```
- 01:00:10 - `install_vector_yum.yml`
```yml
# install_vector_yum.yml




```




- 01:00:25 - тест запуска при сломанном чем-то. Пояснение про то, как можно в Докер-контейнере прикрутить systemd с помощью Сигруп. Как альтернатива - использовать Podman.
- 01:02:40 - показана ошибка `CRITICAL Ansible return code was 2, command was:`, а дпльше только стадии cleanup, destroy
- 01:02:56 - !!! Команда `molecule converge`. Как прогнать тест, но не уничтожить ВМ. 
- 01:05:20 - итог прогонки `molecule converge`
- 01:05:30 - !!! посмотреть что там на ВМ делается. Команда `molecule login --host <hostname> `. Зашли под пользователем cloud-user, под которым работает инвентори. И дальше можно тестировать.
- 01:06:05 - запуск команды внутри новой ВМ
- 01:06:15 - делаем тесты
- 01:07:10 - !!! пояснение как работать для CI на локальной ОС. После изменений конфигурации нужно запускать `molecule converge`. Он пытается прогнать роль. Она отваливается по каким-то причинам. Можно зайти на ВМ по `molecule login`, посмтореть что там ив каком состоянии, емли по логу молекулы не понятно что и где отвалилось (каких-то прав не хватило или еще что-нибудь)
- 01:08:30 - делаем фиксы, converge, фиксы, converge, фиксы, converge. А потом когда все хорошо стало, делаем коммит, а там уже при появлении новой ревизии автоматом запускается в системе оркестратора конвейер сборки и тестирования, ну или молекульные тесты. Вытягивает из репозитория и запускает молекул тест
- 01:08:35 - как сделать коммит.
- 01:09:00 - об использовании `hint` в виде файла `pre-commt-config.yml`. Рекомендация сайта [pre-commit.com](https://pre-commit.com). Выполняется прекомит чек
и есть уверенность на 90%, что мы линты пройдем. Останавливается коммит при наличии ошибок и даже не дасть сделать коммит с ошибкой. А также ошибки могут автоматически исправляться.
```yml
repos:
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: 3.2.0
  hooks:
  -   id: trailing-whitespace
  -   id: end-of-file-fixer
  -   id: check_yaml
  -   id: check-added-lirge-files
```
- 01:10:25 - Файл `install_vector.yml`
```
---
- name: Install Vector | Download RPM

```
- 01:11:20 - получается, что коммиты хорошо и качественно протестированы на разном окружении на manage_node.


* Про TOX
- 01:11:45 - о том, что на `manage_node` и` control_node` бвыает разные версии Ансибл, а этот разный Ансибл может управляться разными Пайтонами. Чтобы этот зоопарк оттестировать можно испоьзовать Tox. Этот фреймворк связан не с Ансибл, а с Пайтон. tox – менеджер виртуальных окружений. Он создает ENV, в них накидывает пакеты, которые нам нужны. Причем разные версии пакетов. Он умеет их завпускать против разных Пайтонов, которые мы ему  скажем. Но они все должны быть установлены в моей системе. А в конце он запустит ту команду, которую мы укажем ему в аргументе. Например, запустить Пайтон.
- 01:13:10 - Тох нужен для подготовки разных окружений и запуска молекулы, которая запускает сценарии тестирования внутри разных Ансибл на `control_node`

### 15Tox     01:11:45
Данный фреймворк позволяет проводить тестирование любого
python кода, грубо говоря, tox – менеджер виртуальных окружений:
- Выбирать, какой версии python использовать (версии должны быть установлены в системе);
- Выбирать, какие дополнительные модули должны быть установлены;
- Тестировать разные версии модулей друг против друга (создавать матрицы тестирования).
Официальная документация [тут](https://tox.readthedocs.io/en/latest/)

- 01:14:15 - как пробрасывать ключи `ssh`. Пример конфигурации файла `create.yml`. Тестовый запуск и пояснения.
- 01:20:00 - как правльно устанавливать TOX

### 16Установка Tox     01:21:20
Так как он является стандартной библиотекой python кода (как и
ansible), то его установка происходит достаточно просто:
- pip3 install tox
- pip install tox

- 01:22:00 - !!! надо отдельно устанавлвать модуль `molecule_docker`. `pip3 install molecule_docker`

### 17Настройка Tox     01:22:27
В нашем случае, нам необходимо два файла для использования tox:
- tox.ini – основной файл настройки, содержит в себе
перечисление python и возможных модулей плюс описание их
прменения друг против друга, а также указание команды,
которую необходимо выполнить для проведения тестирования
- test-requirements.txt – перечисление дополнительных модулей,
которые не должны иметь итерирования против разных версий
python.

### 18Запуск Tox      01:22:52
- tox – запуск на всех возможных окружениях,
- tox -l – показ всех возможных окружений,
- tox -r – принудительное пересоздание окружений,
- tox -e <env_names> – запуск на указанных окружениях

- 01:22:55 - показан файл `tox.ini`
- 01:23:10 - разбор содержимого файла `tox.ini`

```
[tox]
env_path =          # здесь можно запсать путь до дректории, где будет лежать кэш   -01:45:50
minversion = 1.8
asepython = 3.6
envlist = py{36,39}-ansile{28,30}
# envlist = py{36,39}-ansile{28,30}-molecule-molecule_docker    #варант записи
skipsdist = true

[testenx]           # даем указане что нужноустановить в ОС. каке версии ansile
passenv = *         # для того, чтобы был разрешены все енвы хоста  - 01:44:50
deps =
    -rtest.requirements.txt
    ansile28: ansile<2.9
    ansile29: ansile<2.10
    ansile210: ansile<3.0
    ansile38: ansile<3.1
commands :          # Указание какую команду надо запустть
    {posargs:molecule --version}
    {posargs:molecule test -s alternative --destroy=always}     # --destroy=always    за собой всегда удалять
                                                                # test -s alternativ  запуск сценаря с именем alternativ    - 01:27:00
    
    
```
* `test.requirements.txt`
```
molecule++3.4.0
molecule_docker     # !!! модуль для того, чтобы молекула поднимала Докер
docker
ansile_lint
yamlint

```

- 01:27:20 - почему у нас два разных сценария `default`, `alternative`. Т.к тох очень медленно работает, т.к. ему надо очень много скачать и установть в ОС, то делаем легкий сценарий альтернативный, где некоторые компоненты отсутствуют (в общем для быстрого тестирования)
- 01:29:40 - тох запускается на сборки PullRequest
- 01:30:45 - про работу с образами Docker
- 01:31:04 - пример файла Dockerfile

```
FROM registry.redhut.io/rhel18/podman:latest
ENV MOLECULE_NO_LOG false

RUN yum reinstall glibc-common -y
RUN yum update -y && yum install tar gcc make python3-pip zlib-devel openssl-devel yum-utils libffi-devel -y

ADD https://www.python.org/ftp/python/3.6.13/Python-3.6.13.tgz Python-3.6.13.tgz
RUN tar xf Python-3.6.13.tgz && cd Python-3.6.13/ && ./configure && make && make altinstall
ADD https://www.python.org/ftp/python/3.7.10/Python-3.7.10.tgz Python-3.7.10.tgz
RUN tar xf Python-3.7.10.tgz && cd Python-3.7.10/ && ./configure && make && make altinstall
ADD https://www.python.org/ftp/python/3.8.8/Python-3.8.8.tgz Python-3.8.8.tgz
RUN tar xf Python-3.8.8.tgz && cd Python-3.8.8/ && ./configure && make && make altinstall
ADD https://www.python.org/ftp/python/3.9.2/Python-3.9.2.tgz Python-3.9.2.tgz
RUN tar xf Python-3.9.2.tgz && cd Python-3.9.2/ && ./configure && make && make altinstall
RUN python3 -m pip install --upgrade pip && pip3 install tox selinux
RUN rm -rf Python-*
```
- 01:32:04 - запуск `docker run -v` и сразу вход в терминал контейнера

```
docker run -v $(pwd):/opt/elasitcsearch_role -it tox_docker:latest /bin/bash
``` 
Зашли внутрь контейнера. Команды проверки:

```
python && tox --version &&
```

- 01:34:15 - по командам запуска
  - tox  - создает окружение
  - tox -l - показ всех возможных окруженй
  - tox -r - принудительное пересоздание окружения
  - tox -e <env_name> - запуск на указанных окружениях

- 01:35:30 - tox -l
```
py36-ansible28
py36-ansible30
py96-ansible28
py96-ansible30
```
- 01:35:50 - команда tox. И что происходит

```
py36-ansible28 create: /opt/elasticsearch_role/.tox/py36-ansible28
py36-ansible28 installdeps: -rtest-requirements.txt, ansible<2.9
```

- 01:36:42 -!!! надо было проверить что установлен Docker
- 01:40:40 - запуск тох с альтернатвным сценарием
- 01:41:35 - !!! как создать и подогреть кэш. В файле `tox.ini` создать 
``` 
commands = 
    {posargs:python -V}

```
- и потом запустить команду `tox -p5` . !!! Так TOX создаст окружение и оно будет лежать в кеше. Но кэши работают, пока нчего не меняется в конфгурац  наборе версий ансибле, докер  т.д. Поменяется что-нибудь (кроме команд) и все, кэш будет создаваться заново.  Но команды можно записывать любые

- 01:42:55 - пояснение как запустить несколько ТОХ в параллель
- 01:46:00 - !!! при запуске TOX создается окружение, потом молекула создает ВМ (докер конткйнеры)
- 01:47:10 - !!! запуск команды `molecule test` и пояснение
- 01:50:50 - !!! запуск команды `molecule converge` и пояснение
- 01:53:55 - !!! запуск команды `molecule login` и пояснение. Входим внутрь контейнера
- 01:56:10 - пояснение по хендлеру и докер-контейнеру.на примере файла `main.yml`
- 01:56:25 - !!! пояснение по ДЗ как сделать тест, при котором все роли друг с другом взаимодействуют
В Dockerfile с помощью CMD запустить Elasticsearch  пусть он там рботает. Этого достаточно для выполненя ДЗ. А дальше тестируйте Elastic.
К сожалению нет в ansile модулей для Яндекс.Облака, а то бы мы могли их использовать

- 01:58:53 - запуск команды `molecule destroy`
- 01:59:30 - пояснение о том, что молекулу можно использовать как эмулятор поведения терраформ


### 19Итоги

### 20Итоги     01:58:40
Сегодня мы узнали, что:
- существует удобный framework для тестирования – molecule;
- molecule позволяет проверить работоспособность роли против
разного окружения на managed node;
- второй framework для тестирования – tox;
- tox позволяет проверить работоспособность роли против
разного окружения на control node.

- Vagrant можно подкрутить через драйвер Delegate

### Практическая часть лекции

### 21Домашнее задание      02:03:50
Давайте посмотрим ваше домашнее задание.
- Вопросы по домашней работе задавайте в чате мессенджера
Slack.
- Задачи можно сдавать по частям.
- Зачёт по домашней работе проставляется после того, как приняты
все задачи.

* Наша основная цель - настроить тестирование наших ролей. 
  - Задача: сделать сценарии тестирования для kibana, filebeat. 
  - Ожидаемый результат: все сценарии успешно проходят тестирование ролей.

- 02:03:50 - по предврительной подготовке. Нужно создать Докер-образ для дальнейшего тестирования ТОХ
- 02:04:00 - версию молекулы можно использовать и 3.5.2 и последнюю
- 02:04:25 - пояснение как делать.
  - Нам надо протетировать те роли, которые мы приготовили на прошлом ДЗ
  - Зайти в каждую роль и запускать команду `molecule init scenario ` и указать с каким драйвером этот сценарий сделать. 
  - Команда `molecule init scenario --driver-name docker`
  - Ожидаемый результат - это все сценарии успешно проходят и все у них хорошо

- 02:04:55 - сначала скачать роль `elasticsearch` и прогнать по ней тест. Запустить `molecule test` внутр корневой дректории. Для этого надо чтобы был установлен Докер, молекула и `molecule_docker` пакет. 
  - Смотрим вывод команд. Там все должно быть хорошо
- 02:05:13 - по задача №2. В каталоге с кибана ролью создаем каталог сценаря тестирования при помощ такой команды `molecule init scenario --driver-name docker`
- 02:05:37 - по задаче №3. 
  - Добавьте поочередно несколько разных дистрибутивов (centos:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
  - сначала с centos:8,
  - потом с ubuntu:latest 
- 02:05:58 - по задаче №4
  - Добавьте несколько assert'ов в verify.yml файл, для проверки работоспособности kibana-role (проверка, что web отвечает, проверка логов, etc).`curl localhost:5601` или проверить что файл логов кибаны существует.
  - Запустите тестирование роли повторно и проверьте, что оно прошло успешно. 
- 02:06:33 - по задаче №5
- 02:06:40 - по задаче №6

- 02:06:50 - по ТОХ задача №1
  - Собрали образ из Dockerfile
  - Запустите `docker run --privileged=True -v <path_to_repo>:/opt/elasticsearch-role -w /opt/elasticsearch-role -it <image_name> /bin/bash`, где path_to_repo - путь до корня репозитория с elasticsearch-role на вашей файловой системе.
    - `--privileged=True` для того чтобы podman мог работать
    - `<image_name> ` имя локального образа
- 02:07:17 - по ТОХ задача №2
  - Внутри контейнера выполните команду tox, посмотрите на вывод. Можно долго  с разным не ждать.
- 02:08:00 - по ТОХ задача №3. 
  - Добавьте файл tox.ini в корень репозитория каждой своей роли.
  - Файл `tox.ini` можно взять из тот, что приложен к ДЗ из репо с Elasticsearch mnt-homeworks-
- 02:08:05 - по ТОХ задача №4. `molecule test -s <name_scenery>`
 - Создайте облегчённый сценарий для molecule. Проверьте его на исполнимость.
 - В облегченном сяенарии должно быть destroy, create, converge, destroy
 - `molecule test -s <name_scenery_alternative>` 
- 02:08:55 - по ТОХ задача №5
  - Пропишите правильную команду в tox.ini для того чтобы запускался облегчённый сценарий.
  - alternative
- 02:08:33 - по ТОХ задача №6
  - Запустите docker контейнер так, чтобы внутри оказались обе ваши роли.
  - Volumes? `-v <path_to_repo>:/opt/elasticsearch-role`
- 02:08:40 - по ТОХ задача №7
  - Зайдти поочерёдно в каждую из них и запустите команду tox. Убедитесь, что всё отработало успешно.
    - зайти в контейнер  и там запустить тесты
- 02:08:50 - После выполнения у вас должно получится
  -  два сценария molecule и один tox.ini файл в каждом репозитории.  И test-requirements.txt
  -  Ссылки на репозитории являются ответами на домашнее задание. 
  -  Не забудьте указать в ответе теги решений Tox и Molecule заданий.


- 02:12:00 - пояснения по `assert` с примером файла `molecule/default/verify.yml` где показана проверка того, что в фраза `debag message` пристутствует в фале `debag_msg.message`
- Чтобы Vector что-то передать, нужен Netcad (nc). В примере nc устанавливается и передает на сервер Vector сообщение `debag message` через порт 6767 на адрес 127.0.0.1. Сервер Vector запсывает сообщене в local.log


* Выдержка из файла `verify.yml`
```
task:
  - name: Install nc
    become: true
    yum:
      name: nc
      state: present
  - name: Run debug
    shell: set -o pipefail && echo "deug message" | nc 127.0.0.1:6767
    register: qwe
    change_when: false
  - name: Check local work correctly
    slurp:                              # вычbтываем файл и записываем в переменную mounts
      src: "{{ vector_config_dir }}/local.log"
    register: mounts    
  - name: set correct facts
    set_fact:                           # извлекаем из переменной mounts и ключа contents и декодим из b64 в нормальный вид
      debug_msg: "{{ mounts['content'] | b64decode }}"    # записывается в переменную
  - name: Ensure message is sent
    assert:                             # прверяем что сообщение  debug message присутствует в файле debug_msg.massage
      that:
        - "'debug message' in debug_msg.massage"    # если присутствует, то все хорошо
  
  # И там дальше по аналогии идет проверка что первый вектор получает сообщене от второго вектора
      
```


### 22Задавайте вопросы и
пишите отзыв о лекции!
Алексей Метляков
Алексей Метляков
