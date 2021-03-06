### Лекция по теме "Зачем и что нужно мониторить"

Сергей Андрюнин

### 2План занятия
1. Что такое мониторинг
2. Зачем нужен мониторинг
3. Типы мониторинга
4. Деление мониторинга по доменам ответственности
5. Подходы при настройке мониторинга
6. Подходы при настройке мониторинга: SRE
7. Подходы при настройке мониторинга: логи не нужны
8. Итоги
9. Домашнее задание

### 3Что такое мониторинг?

### 4Что такое мониторинг?
Мониторинг - это сбор, обработка,
агрегирование и отображение в
реальном времени количественных и
качественных показателей системы.
Мониторинг позволяет улучшать,
либо оставлять на приемлемом
уровне качество обслуживания
пользователей.
Взято с сайта: unsplash.com

### 5Что такое мониторинг?
На практике мониторинг чаще всего включает в себя:
● Оценку работоспособности ПО. Например, количество успешных
ответов http-сервера.
● Оценку работоспособности оборудования. Например, средняя
загрузка ЦПУ за 5 минут.
● Бизнес мониторинг. Например, мониторинг движения финансовых
средств.
● Мониторинг безопасности ИС. Например, мониторинг актуальности
каких-либо сертификатов.

### 6Зачем нужен мониторинг

### 7Зачем нужен мониторинг
● Анализ долгосрочных тенденций. Получение качественных
характеристик в обеспечение дальнейшей работоспособности системы.
Например размер БД и его близость к критическим значениям.
● Сравнение версий ПО. Насколько изменения ПО повлияли на качество
обслуживания.
● Оповещение. Превентивное выявление возможных отклонений в
качестве обслуживания и увеличение скорости реакции на сбои.
● Телеметрия текущей работоспособности приложения. Получение
текущей оценки характеристик информационной системы в режиме
real-time.
● Ретроспективный анализ, Выявление узких мест информационной
системы на основе полученных ранее данных мониторинга.

### 8Типы мониторинга
### 9Типы мониторинга
Чаще всего мониторинг разделяют на 2 типа:
● White-box monitoring. Наблюдение за
системой “изнутри”. Сбор данных
профилирования, логов, системные журналы.
● Black-box monitoring. Наблюдение, видимое
извне. Например сбор возникших ошибок
системы технической поддержкой или
отделом тестирования.
### 10Типы мониторинга
Для DevOps-подхода важнее white-box
мониторинг, так как только он позволяет
спрогнозировать поведение системы и
исправить проблемы до их возникновения.
Но про black-box мониторинг тоже не стоит
забывать. Это “симптоматическая”
диагностика, которая позволяет выявить
редкие кейсы поломок системы.

### 11Деление мониторинга по
доменам ответственности

### 12Деление мониторинга по доменам
ответственности
Домены ответственности мониторинга можно разделить
по системам сбора метрик:
- Система сбора временных рядов. Prometheus, Inﬂuxdb.
- Система сбора логов. ELK. Это решение на Java. Оно уже устаревает. Кроме того, есть более производительные системы: Gafana, Grafana Agent
- Система перехватчик-ошибок. Sentry он написан на PHP, New Relic - работает с Ruby, Python. Главное - подключить библиотеку и использовать SDK.
> Sentry. Можно создать промис-решение, когда мы берем бинари, раскатываем у себя на своем железе и можно пользоваться всем максимальным функционалом какой там есть и бесплатно. В New Relic так не получится.
> Зато в New Relic есть большой плюс - можно собирать в него метрики для мониторинга с агентов. Им можно, если подключить бибилиотеку собирать стэктрейс, ошибки, логи. Подходит для небольших проектов.

### 13Деление мониторинга по доменам ответственности     - 00:31:59

Системы сбора временных рядов
позволяют получать телеметрию в
режиме реального времени и хранить
данные в таблицах в виде “метка-
времени - значение”.
Такие системы нужны для
непрерывного наблюдения за
изменением в работоспособности
системы.
Пример метрики - доступное место на файловой системе.
Можно собирать бизнесовые метрики SLA, SLO ...

### 14Деление мониторинга по доменам ответственности     - 00:33:47

Основные метрики для мониторинга
в системах временных-рядов:
- CPU LA.
- RAM/swap
- IOPS
- inodes - очень важная метрика в мониторинге Linux
- FS
Взято с сайта: unsplash.com

### 15Деление мониторинга по доменам ответственности     - 00:36:18
Дополнительные метрики для
мониторинга в системах
временных-рядов:
- Process liveness
- IOwait
- Net trafﬁc
- Custom?
Взято с сайта: unsplash.com

### 16Деление мониторинга по доменам ответственности     - 00:36:40
Системы сбора логов позволяют агрегировать логи приложений,
системные журналы и т.п.
Такие системы чаще всего нужны для детального разбора
появившихся проблем, нахождения их частоты появления,
разбора поведения системы в каких-то условиях.
Пример - логи балансировщика запросов.

### 17Деление мониторинга по доменам ответственности     - 00:42:07
Взято с сайта: elastic.co
Пример с Kibana и Logstash (стэк ELK). Может собирать логи, анализировать текстовые данные, с кнопочками, Блэк Джеком и куртизанками.

### 18Деление мониторинга по доменам ответственности    - 00:43:05
Системы “Перехватчик ошибок” позволяют информировать о
возникающих ошибках ПО в режиме real-time.
При этом для этой системы можно указать дополнительные
характеристики, которые будут в информировании.
Например:
- Слой (stage/production).
- Hostname.
- Номер реплики приложения.
- Внутреннее состояние приложения.



### 19Деление мониторинга по доменам ответственности    - 00:45:35
Взято с сайта: docs.sentry.io

### 20Деление мониторинга по доменам ответственности
Основные метрики для мониторинга в системах
сбора логов и ловли ошибок:
- Good events/All events
- Trafﬁc

### 21Подходы при настройке мониторинга    - 00:48:40

### 22Подходы при настройке мониторинга
Приведенные подходы не являются формальными и не будут тем
самым “золотым молотком”, который решит все проблемы.
Но желательно эти подходы знать и использовать в своей
деятельности по необходимости.
Они позволяют экономить на ресурсах, выделенных для
мониторинга, связать мониторинг с бизнес частью и улучшить
качественную интерпретацию полученных метрик.

### 23Подходы при настройке мониторинга: SRE
SRE  - Site Realbility Engeneering


### 24Подходы при настройке мониторинга: SRE
Данный подход разработан компанией
google и повсеместно внедряется в
компаниях по всему миру.
Подход SRE покрывает большинство
задач DevOps, в т.ч. задачи
мониторинга.
Один из его главных плюсов - он
сделан практикующими инженерами
для практикующих инженеров, на
основе многолетнего опыта.
Взято с сайта: landing.google.com

Рекомендация книги
- [Бейер, Джоунс, Петофф: Site Reliability Engineering. Надежность и безотказность как в Google](https://www.labirint.ru/books/656404/) 
- [Розенталь, Джонс: Хаос-инжиниринг](https://www.labirint.ru/books/766759/)

### 25Подходы при настройке мониторинга: SRE      - 00:53:25
Основные тезисы подхода при настройке мониторинга:
- Ставим для мониторинга реальные задачи.
- Система мониторинга должна отвечать на четыре "Золотых" вопроса: 
  - что сломалось? 
  - где сломалось?
  - почему сломалось?
  - когда сломалось?

- Используйте четыре золотых сигнала.
- Не исследуйте только средние значения.
- Выберите подходящий уровень детализации.

### 26Подходы при настройке мониторинга: SRE      - 00:56:58
Четырьмя золотыми сигналами в подходе SRE называются:
- Время отклика. Время, которое требуется для выполнения запроса.
- Величина трафика. Величина нагрузки, которая приходится на вашу
систему. Например для веб - это количество http запросов, а для
потокового аудио - это скорость передачи данных.
- Уровень ошибок. Количество или частота неуспешно выполненных
запросов. Например ответ 500 от http сервера.
- Степень загруженности. Показатель того, насколько полно загружен
ваш сервис. Это мониторинг компонентов, которые покажут
загруженность вашего сервиса. Например для вычислений - это ЦПУ.
Для In-memory БД - это RAM.

### 27Подходы при настройке мониторинга: SRE      - 01:00:00
Кроме golden-signals также существуют такие важные метрики, как:
- SLO. Целевой уровень качества обслуживания. Целевое значение или диапазон значений.
- SLA. Соглашение об уровне обслуживания. Явный или неявный контракт с внешними пользователями, включающий в себя последствия невыполнения SLO.
- SLI. Индикатор качества обслуживания. Конкретная величина предоставляемого обслуживания.

Рекомендован ресурс [Управление инцидентами SLA, SLO и SLI: в чем разница?](https://www.atlassian.com/ru/incident-management/kpis/sla-vs-slo-vs-sli)

### 28Подходы при настройке мониторинга: SRE
Метрики SLO/SLA/SLI позволяют связать измеряемые “технические”
значения с бизнес составляющей.
Пример простого SLA:
Если наш сайт отдает http коды 4хх/5xx, то мы должны произвести
денежную компенсацию пользователю, если это не запланированное
техническое обслуживание.
Пример SLO:
В 99% случаев мы должны отдавать http коды отличный от 4xx/5xx. 1%
выделяется на техническое обслуживание.
Расчет SLI может выглядеть таким образом:
SLI = (summ_2xx_requests + summ_3xx_requests)/(summ_all_requests)

### 29Подходы при настройке мониторинга: логи не нужны    - 01:04:00

### 30Подходы при настройке мониторинга: Логи не нужны
Данный подход предложен компанией “Яндекс”
на DevOpsConf 2019.
Формулируется данный подход следующим образом:
“Достаточно создать инфраструктуру доставки полезных
метрик и данных вокруг сервисов. В результате появятся
полезные метрики, которые говорят о сервисах, и прозрачно
показывают все, что с ними происходит.”

Идея такая: логи можно обернуть в метрики и из этих метрик пострить шкалу временных рядов с какими-то конкретными значениями и,
таким образом наличие самих логов нам не нужно. Например, если возникают ошибки обработки какого-то запроса мы можем создать метрику ошибки этого запроса
и уже считать количество этой новой метрики

### 31Подходы при настройке мониторинга: Логи не нужны    - 01:06:20
Основные тезисы данного подхода:
- Логи в сыром виде бесполезны, но их можно превратить в полезные метрики.
- Ошибки должны содержать контекст самой ошибки. - Это про Sentry

Tools for Elastic - ElastAlert. Она умеет читать Elastic и строить Prometeus метрики на основании этого.

### 32Итоги    - 01:09:00

### 33Итоги
Мониторинг - неотъемлемая часть обеспечения качества
информационных систем.
Мониторинг должен:
- Отвечать на вопросы “Что случилось?” и “Почему?”.
- Быть достаточно простым.
- Иметь подходящий уровень детализации
- Содержать в себе метрики, связанные с “бизнес” частью

- 01:13:08 - ответ на вопрос про готовый инструмент для мониторинга резервного копирования. 

Ответ про то как сделано у Андрюнина: 
- ПО [WAL-G](https://habr.com/ru/post/506610/) для резервного копирования СУБД PostgreSQL 
- [WinBackup](https://winbackup.ru.uptodown.com/windows) - прекрасно отдает метрики.


### 34Домашнее задание      - 01:17:00
Давайте посмотрим ваше домашнее задание.
- Вопросы по домашней работе задавайте в чате мессенджера Slack.
- Задачи можно сдавать по частям.
- Зачёт по домашней работе проставляется после того, как приняты все задачи.

- 01:30:57 - практическая часть лекции

- 01:33:40 - go mod download - утилита для создания нагрузки http
- 01:36:50 - вход в графану
- 01:38:40 - показан Prometeus. Метрики в формате Prometeus
- 01:39:20 - таблица с данными SQL

Демо из ДЗ: [Caching in Go: Redis](https://github.com/hamnsk/go_psql_redis_example)

Все свои проекты Андрюнини подключает к [SonarQube](https://www.sonarqube.org/) для проверки качества кода и на уязвимость.

Рекомендация книги 

[BPF для мониторинга Linux](https://www.ozon.ru/product/bpf-dlya-monitoringa-linux-kalavera-devid-fontana-lorentso-184708611/?sh=5vavedyE1w)
[BPF для мониторинга Linux](https://www.labirint.ru/books/758839/)

- 02:23:20 - Mikrotik + SNMP. SNMP exporter. Пример мониторинга в Prometheus.




### 35Задавайте вопросы и
пишите отзыв о лекции!
Сергей Андрюнин
Сергей Андрюнин
