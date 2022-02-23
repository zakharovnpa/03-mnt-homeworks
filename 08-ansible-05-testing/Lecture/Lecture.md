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
- Создавать scenarios тестирования;
- Тестировать roles против разного окружения;
- Поддерживает docker, podman, delegated как драйверы подключений.
Официальная документация [тут](https://molecule.readthedocs.io/en/stable-1.18/index.html#)

### 5Как создать Role?      00:16:05
- Создать стандартную структуру директорий и файлов при
помощи molecule;
- Создать необходимые tasks, handlers;
- Определить все необходимые переменные в defaults и vars;
- Создать готовый тестовый playbook в tests, заполнить файлы molecule для проведения тестирования;
- Заполнить meta всю информацию о роли, наиболее полно описать её в README.md.

### 6Структура директорий   00:16:59
После инициализации новой роли мы получаем следующие
директории и файлы:
- стандартный набор директорий и файлов для role;
- molecule – набор scenarios для тестирования.
Внутри любого scenario находятся следующие файлы:
- molecule.yml – основной файл для molecule;
- converge.yml – playbook, который molecule будет использовать для запуска тестов;
- verify.yml – дополнительные тесты после исполнения role.


- Команда запуска по созданию новой роли молекулой `molecule init role new_role`. Появится новая директория `new_role`
Помимо всего появилась директория `/molecule` и `default`. `default` - это имя сценария тестирования.
- 00:18:50 - описание файлов в сценарии тестирования
- 00:19:20 -`molecule.yml`
- 00:24:10 -`create.yml`, `destroy.yml`
- 00:26:20 -`converge.yml`- это просто плейбук, чтобы запустить нашу роль
Зайти в директорию с ролью и запустить команду `molecule init scenario open --driver openstack`
- 00:29:05 - появилась новая директория `open`. Это наш новый отдельный сценарий
- 00:29:35 - описание файла сценария `molecule.yml`
- 00:29:57 - описание файла сценария `create.yml`
- 00:31:00 - описание файла сценария `destroy.yml`
- 00:31:05 - описание файла сценария `prepare.yml`
- 00:32:00 - о сценариях для  Докера
- 00:32:30 - о том, как включить работу молекулы с файлом Dockerfile
- 00:33:27 - о папке `/alternative`
- 00:33:45 - подытог

### 7Структура molecule.yml   00:34:23
Внутри файла находятся следующие директивы:
- dependency – перечисление зависимостей роли;
- driver – указание параметров выбранного driver;
- platform – перечисление хостов для выбранного driver;
- provisioner – указание поставщика для molecule;
- veriﬁer – выбор framework для проведения проверок.
Туда же можно добавить:
- lint – конфигурирование linter для тестирования;
- scenario – перечисление сценариев тестирования.

### 8Как правильно тестировать Role?   00:35:04
В данном виде тестирование role упрощается, так как нужно:
- Подготовить тестовое окружение;
- Запустить сценарий проверки через `molecule test`;
- Исправить ошибки на каждом из этих этапов и повторять весь
сценарий, пока не будет получен положительный результат.

### 9Как правильно тестировать Role?   00:35:50
Тестирование условно можно разделить на три вида:
- Использование полного сценария тестирования;
- Использование собственных сценариев тестирования;
- Использование отдельных частей сценария самостоятельно.

### 10Как правильно тестировать Role?   00:36:00
molecule test запускает полный сценарий тестирования role.
Полный сценарий включает в себя:
- запуск команды `molecile test`

Затем молекула пытается:
- lint – прогон линтеров;
- destroy – удаление старых инстансов с прошлого запуска;
- dependency – производит установку ansible-зависимостей, если есть;
- syntax – проверка синтаксиса с помощью `ansible-playbook --syntax-check`;
- create – создание инстансов для тестирования;
- prepare – подготовка инстансов, если это необходимо.

### 11Как правильно тестировать Role?        00:37:30
`molecule test` запускает полный сценарий тестирования role.
Полный сценарий включает в себя:
- converge – запуск тестируемого плейбука;
- idempotence – проверка на идемпотентность при помощи повторного запуска;
- side_effects – действия, которые не относятся к role, но необходимые для тестирования;
- verify – запуск тестов с помощью указанного фреймворка тестирования;
- cleanup – очистка внешней инфраструктуры от результатов тестирования;
- destroy – уничтожение инстансов для тестирования.

### 12Как правильно тестировать Role?        00:40:35 и    00:42:30
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
-  00:40:40 - когда мы запускаем `vklecule converge`, то запускается он не сразу, а сначала запустятся те таски, от которых этот `converge` зависит.
-  00:40:40 - Для того чтобы понимать, что каждая из tasks будет запускать, в случае отдельного вызова – необходимо пользоваться 
конструкцией `molecule matrix <task_name>`.
Пример: 
`molecule-matrix converge`
`molecule-matrix converge -s open`  -выдаст только для open
`molecule-matrix converge` - не удаляет за собой инстансы. Он прогонит роль, и можно к этим ВМ подключиться и посмотреть чсто там. 
- 00:44:30 - Причем подключиться можно тоже через молекулу. `molecule login -s <scenery_name> --host <hostname>`
- 00:45:10 - Оставить возможность подключения можно и с полным сценарием тестирования, воспользовавшись параметром `--destroy=never`

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

### 14Список основных команд molecule     00:47:25
```ps
- molecule init role --driver-name <driver> <rolename>
```
```ps
- molecule init scenario --driver-name <driver> <scenarioname>
```
```ps
- molecule test
```ps
```
- molecule test --destroy=never
```
```ps
- molecule matrix <taskname>
```
```ps
- molecule matrix -s <scenarioname> <taskname>
```
```ps
- molecule <taskname>
```
- 00:48:00 - показ эксклюзива. Работа молекулы с облаками. Описание файла `molecule.yml`
- 00:52:56 - Описание файла `destroy.yml`
- 00:53:29 - Описание файла `verify.yml`
- 00:55:27- запуск команды `molecule test` и пояснение что происходит
- 01:00:00 - тест запуска при сломанном чем-то. Пояснение про то, как можно в Докер-контейнере прикрутить systemd с помощью Сигруп.
- 01:02:56 - как прогнать тест, но не уничтожить ВМ. Команда `molecule converge`
- 01:05:20 - итог прогонки `molecule converge`
- 01:05:30 - посмотреть что там на ВМ делается. Команда `molecule login --host <hostname> `. И дальше можно тестировать.
- 01:08:35 - как сделать коммит.
- 01:09:00 - об использовании `hint` в виде файла `pre-commt-config.yml`. Реклмендация сайта ]pre-commit.com](https://pre-commit.com). Выполняется прекомит чек
и есть уверенность на 90%, что мы линты пройдем.
- 01:11:45 - о том, что на ` control_node` бвыает разные версии Ансибл, а этот разный Ансибл может управляться разными Пайтонами. Чтобы этот зоопарк оттестировать можно испоьзовать Tox. Этот фреймворк связан не с Ансибл, а с Пайтон. tox – менеджер виртуальных окружений. Он создает ENV, в них накидывает пакеты, которые нам нужны. Причем разные версии пакетов. Он умеет их завпускать против разных Пайтонов, которые мы ему  скажем. Но они все должны быть установлены в моей системе. А в конце он запустит ту команду, которую мы укажем ему в аргументе. Например, запустить Пайтон.
- 01:13:10 - Тох нужен для подготовки разных окружений и запуска молекулы, которая запускает сценарии тестирования внутри разных Ансибл на `control_node`

### 15Tox     01:11:45
Данный фреймворк позволяет проводить тестирование любого
python кода, грубо говоря, tox – менеджер виртуальных окружений:
- Выбирать, какой версии python использовать (версии должны быть установлены в системе);
- Выбирать, какие дополнительные модули должны быть установлены;
- Тестировать разные версии модулей друг против друга (создавать матрицы тестирования).
Официальная документация [тут](https://tox.readthedocs.io/en/latest/)

- 01:14:15 - как пробрасывать ключи `ssh`. Пример конфигурации файла `create.yml`. Тестовый запуск и пояснения.

### 16Установка Tox     01:21:20
Так как он является стандартной библиотекой python кода (как и
ansible), то его установка происходит достаточно просто:
- pip3 install tox
- pip install tox

### 17Настройка Tox     01:22:27
В нашем случае, нам необходимо два файла для использования tox:
- tox.ini – основной файл настройки, содержит в себе
перечисление python и возможных модулей плюс описание их
перечисления друг против друга, а также указание команды,
которую необходимо выполнить для проведения тестирования
- test-requirements.txt – перечисление дополнительных модулей,
которые не должны иметь итерирования против разных версий
python.

### 18Запуск Tox      01:22:52
- tox – запуск на всех возможных окружениях,
- tox -l – показ всех возможных окружений,
- tox -r – принудительное пересоздание окружений,
- tox -e <env_names> – запуск на указанных окружениях

- 01:23:10 - разбор содержимого файла `tox.ini`
- 01:30:45 - про работу с образами Docker
- 01:31:04 - пример файла Dockerfile
- 01:32:04 - запуск `docker run -v` и сразу вход в терминал контейнера
- 01:34:15 - по командам запуска
- 01:42:55 - пояснение как запустить несколько ТОХ в параллель
- 01:47:10 - запуск команды `molecule test` и пояснение
- 01:50:50 - запуск команды `molecule converge` и пояснение
- 01:53:55 - запуск команды `molecule login` и пояснение
- 01:56:10 - пояснение по хендлеру и докер-контейнеру.на примере файла `main.yml`
- 01:56:25 - пояснение по ДЗ как сделать тест, при котором все роли друг с другом взаимодействуют
- 01:58:53 - запуск команды `molecule destroy`
- 01:59:30 - пояснение о том, что молекулу можно использовать как эмулятор поведения терраформ


### 19Итоги

### 20Итоги     01:58:40
Сегодня мы узнали, что:
● существует удобный framework для тестирования – molecule;
● molecule позволяет проверить работоспособность роли против
разного окружения на managed node;
● второй framework для тестирования – tox;
● tox позволяет проверить работоспособность роли против
разного окружения на control node.

### Практическая часть лекции

### 21Домашнее задание      02:03:50
Давайте посмотрим ваше домашнее задание.
● Вопросы по домашней работе задавайте в чате мессенджера
Slack.
● Задачи можно сдавать по частям.
● Зачёт по домашней работе проставляется после того, как приняты
все задачи.

- 02:03:50 - по предврительной подготовке. Нужно создать Докер-образ для дальнейшего тестирования ТОХ
- 02:04:25 - пояснение как делать. Запускать команду `molecule init scenario ` и указать с каким драйвером этот сценарий сделать.
- 02:04:55 - сначала скачать роль `elasticsearch` и прогнать по ней тест. Для этого надо чтобы был установлен Докер, молекула и докер-образ. Смотрим вывод команд
- 02:05:13 - по задача №2
- 02:05:37 - по задаче №3
- 02:05:58 - по задаче №4
- 02:06:33 - по задаче №5
- 02:06:40 - по задаче №6
- 02:06:50 - по ТОХ задача №1
- 02:07:17 - по ТОХ задача №2
- 02:08:00 - по ТОХ задача №3. Файл `tox.ini` можно взять из тот, что приложен к ДЗ
- 02:08:05 - по ТОХ задача №4. `molecule test -s <name_scenery>`
- 02:08:55 - по ТОХ задача №5
- 02:08:33 - по ТОХ задача №6
- пояснения по `assert`



### 22Задавайте вопросы и
пишите отзыв о лекции!
Алексей Метляков
Алексей Метляков