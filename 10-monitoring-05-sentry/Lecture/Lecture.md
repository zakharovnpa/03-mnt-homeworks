### Лекция по теме "Платформа мониторинга Sentry"

### Ссылки на лекции:

[Site Sentry](https://sentry.io/welcome/)
[Self-Hosted Sentry nightly](https://github.com/getsentry/self-hosted)
[Official Sentry integration/plugin](https://gist.github.com/denji/b801f19d95b7d7910982c22bb1478f96#file-docker-compose-yml)
[Sentry 10 helm charts](https://github.com/sentry-kubernetes/charts)
[Sentry Configuration](https://develop.sentry.dev/config/)
[sentry.conf.example.py](https://github.com/getsentry/self-hosted/blob/master/sentry/sentry.conf.example.py)


Платформа мониторинга Sentry
Сергей
БывшевСергей Бывшев
Ведущий инженер автоматизации в "Метр
квадратный"
Сергей Бывшев

### 2План занятия
1. Введение
2. Архитектура
3. Конфигурация
4. User Interface
5. Sentry SDK
6. Итоги
7. Домашнее задание

### 3Введение
### 4Введение
Платформа мониторинга приложений Sentry помогает диагностировать,
исправлять и оптимизировать производительность своего кода.
● Механизм работы Sentry основывается на real-time мониторинге
событий в ПО, их сбору, анализу и нотификации.
● Каждое событие в системе Sentry имеет развёрнутое описание,
содержащее метаданные ПО (версия ПО, модуль, stacktrace и т.д.).
● Cобытия можно визуализировать, группируя их. Например, по типу или
по гео-привязке сервисов.
● Чаще всего отслеживаемыми ошибками являются ошибки приложения.

### 5Введение
Для интеграции Sentry в ПО требуется добавить в код специальный
обработчик.
Обработчики поставляются в виде SDK, которые легко добавляются в
различные ЯП или фреймворки.
Всего Sentry поддерживает до 93 различных платформ.
Взято с сайта: sentry.io

### 6Введение
Sentry позволяет провести
интеграцию с различными
системами ведения задач.
Например, Atlassian Jira.
Можно прямо из события Sentry:
● создать задачу,
● привязать исполнителя,
● автоматически заполнить
описание данными события,
● сменить статус задачи
Взято с сайта: sentry.io

### 7Введение
Взято с сайта: sentry.io

### 8Введение
Одним из главных преимуществ Sentry является нотификация о событиях.
Sentry поддерживает множество платформ для нотификации, а также
имеет механизм вебхуков для ваших собственных или не обслуживаемых
систем.
Нотификации можно настраивать гибким образом, выбирая их
чувствительность, частоту повтора, каналы оповещения.

### 9Введение
Взято с сайта: sentry.io

### 10Введение
Таким образом, система Sentry может заменить собой логирование
приложения, ведь вы всегда будете информированы о событиях,
знать их частоту, связанные реплику и версии приложения и т.д.
Взято с сайта: sentry.io

### 11Архитектура

### 12Архитектура
Sentry представляет из себя веб-приложение, которое поставляется в двух
видах:
●
●
cloud
self-hosted
Cloud-архитектура рамещается на серверах sentry.io и
представляет из себя многокомпонентную систему, содержащую:
●
●
●
●
●
Sentry app
PostgreSQL
Redis
Memcached
Kafka + Zookepeer
Аналогичную Cloud-архитектуру возможно использовать и в self-hosted
системе, но это влечет большие затраты по обслуживанию. В то же время,
это бесплатное предоставление всех возможностей системы для
использования, в соответствии с прикрепленной лицензией.

### 13Архитектура
Взято с сайта: sentry.io

### 14Конфигурация      - 00:14:45

- 00:14:45 - развертывание Sentry на локальной ОС

Один из вариантов развертывания: [Self-Hosted Sentry nightly](https://github.com/getsentry/self-hosted)
* Файл `docker-compose.yml`
[docker-compose.yml](https://github.com/getsentry/self-hosted/blob/master/docker-compose.yml)

- 00:16:45 - есть вариант проще:
[Official Sentry integration/plugin](https://gist.github.com/denji/b801f19d95b7d7910982c22bb1478f96#file-docker-compose-yml)

- 00:17:40 - для разорачивания в кубернетисе
[Sentry 10 helm charts](https://github.com/sentry-kubernetes/charts)



### 15Конфигурация    - 00:18:30
Конфигурация Sentry происходит в файлах:
* `conﬁg.yml` - настройка основных параметров работы системы
* `sentry.conf.py` - более расширенная настройка системы
Основную настройку рекомендуется производить в `conﬁg.yml`.

> Если необходимо расширить настройки или функционал, можно
> производить настройку в `sentry.conf.py` на ЯП python v3.

Часть настроек также можно производить из environment переменных
среды.
Данные конфигурационные файлы имеют хорошее внутреннее описание и
простую структуру.

### 16Конфигурация
Пример конфигурации smtp из conﬁg.yaml mail сервера для отправки
нотификаций:

### 17Конфигурация
Пример конфигурации sentry.conf.py для подключения postgreSQL:

### 18Конфигурация      - 00:20:00
Пример конфигурации sentry.conf.py для подключения Redis:

- 00:20:20 - официальная документация:
[Sentry Configuration](https://develop.sentry.dev/config/)

- 00:22:20 - [sentry.conf.example.py](https://github.com/getsentry/self-hosted/blob/master/sentry/sentry.conf.example.py)
- Подключение к БД
- 

### 19User interface      - 00:23:40

### 20User interface
UI предоставляет огромный набор возможностей для гибкой настройки
обработки и мониторинга входящих событий. Мы рассмотрим лишь
основные.
Основной dashboard выглядит следующим образом:

### 21User interface
Переходя в проект, мы видим непосредственно зарегистрированные
события и некоторую статистику:

### 22User interface
Переходя в событие мы видим множество данных, связанных с ним и
возможности интерактивного взаимодействия, как привязка к
исполнителю для решения или проставления признака, что событие
разрешено:

### 23User interface
Следующая важная сущность - это алёрты. Dashboard для алёртов выглядит
следующим образом:

### 24User interface
Если мы перейдем непосредственно в алёрт - мы увидим меню его
настройки, где сможем всегда его отредактировать.

- 00:32:15 -Создание проекта на Python

### 25User interface
Все настройки, как например
интеграции или ACL - мы можем
произвести в боковом меню
Settings.
Если у вас есть свой pet-project в
виде веб сервиса или приложения -
вы можете попробовать подключить
его к Sentry, используя SDK и
поэкспериментировать с
отображаемыми в Sentry данными.

### 26Sentry SDK      - 00:50:40

### 27Sentry SDK
Рассмотрим на примере настройку python приложения для отправки
событий в Sentry.
● установим необходимую библиотеку с SDK:
● во входной точке ПО импортируем sentry SDK, свяжем с библиотекой
логирования logging b произведем настройку отлова событий:

### 28Sentry SDK
Таким образом, мы установили отлов событий типа ERROR и отправку их в
Sentry. Также в дополнительной информации к событиям ERROR, в Sentry
будет отправлена краткая информация о событиях уровня INFO для
диагностики.
Осталось только запустить процесс обработки событий и отправки их в
Sentry.
Для этого во входной точке приложения добавляем следующий блок:
Параметр dsn - предоставляется в UI системы Sentry при создании проекта.
Параметры integrations - это настройки SDK, которые мы произвели ранее.

### 29Sentry SDK
Рассмотрим, что теперь произойдет при следующей очередности событий:
●
●
●
●
●
Зарегистрируется событие error “I am an event”
Событие INFO “I am a breadcrumb” добавится к событию ERROR как
диагностическое
bar=43 добавится в событие как параметр
Событие EXCEPTION “An exception happend” зарегистрируется как
Stacktrace с полным списком системных вызовов
Событие DEBUG “I am ignored” будет проигнорировано и никак не
обработается

### 30Итоги

### 31Итоги
В данной лекции мы узнали:
● Что такое Sentry
● Возможности данного инструмента
● Как выглядит self-hosted и cloud архитектура
● Рассмотрели какими путями можно сконфигурировать Sentry и
изучили некоторые настройки
● Ознакомились с основными элементами интерфейса Sentry
● Увидели как интегрировать ПО с системой Sentry

### 32Домашнее задание
Давайте посмотрим ваше домашнее задание.
● Вопросы по домашней работе задавайте в чате мессенджера Slack.
● Задачи можно сдавать по частям.
● Зачёт по домашней работе проставляется после того, как приняты
все задачи.

### 33Задавайте вопросы и
пишите отзыв о лекции!
Сергей Бывшев
Сергей Бывшев
