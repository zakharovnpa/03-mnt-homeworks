### лекция по теме "Система сбора логов ELK"

* Ссылки на ресурсы из лекции:

[Docs Logstash Reference [8.1] » Input plugins](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)

[Fluent Bit](https://fluentbit.io/)

[Open Distro](https://opendistro.github.io/for-elasticsearch/)

[OpenSearch](https://opensearch.org/)

[Grafana Loki ](https://grafana.com/docs/loki/latest/?pg=oss-loki&plcmt=quick-links)

[Vector by Datadog](https://vector.dev/)

[Logstahs in Dockerub](https://hub.docker.com/r/bitnami/logstash)



### Система сбора логов ELK
Сергей
БывшевСергей Бывшев
Ведущий инженер автоматизации в "Метр квадратный"
Сергей Бывшев

### 2План занятия
1. Введение
2. Logstash
3. Kibana
4. Filebeat
5. Index Lifecycle Management (ILM)
6. Пример политики hot-warm-cold
7. Настройка Elasticsearch
8. Итоги
9. Домашнее задание

### 3Введение

### 4Введение     -00:02:50
Централизованный сбор логов - один из важнейших разделов мониторинга
в современном мире.
Существует множество решений, но основным на текущий момент является
система сбора логов, построенная на основе стэка технологий
Elasticsearch-Logstash-Kibana (ELK).
Данный стэк позволяет “из коробки” получить средство агрегации и
трансформации, хранения и визуализации данных логирования.
Наиболее распространенное решения для построения системы сбора логов
на основе ELK является архитектура “Hot-Warm”.
“Hot-Warm” архитектура представляет из себя кластер Elasticsearch узлов,
каждый из которых имеет свою метку (hot/warm).
* Logstash Можно заменть на Fluend какой-то
* Kibana Можно заменть на Grafana

### 5Введение       -00:04:05
Кластерные узлы, помеченные, как “Hot” осуществляют хранение и сбор
“быстрых” данных логирования, например за последние сутки.
Такие узлы требовательны к скорости операций чтения/записи диска и,
соответственно, хранят данные на SSD-носителях.
В то же время, обычно им не требуется большой размер носителя, так
как время хранения данных на таких узлах небольшое.

### 6Введение
Кластерные узлы, помеченные, как “Warm” осуществляют хранение и
сбор “медленных” данных логирования, например всё что следует за
текущими сутками.
Такие узлы не требовательны к операциям чтения/записи диска, но
требовательны к объёму. Соответственно, хранение данных можно
осуществлять на HDD-носителях.
Также возможно использовать “Cold” узлы, которые, например, хранят
данные старше 30 дней.

### 7Введение     -00:05:45
Взято с сайта: elastic.co

-00:06:10 - о ролях в кластере ELK
* Node roles:
  * master - master кластрера
  * data - хост с БД не в кластере. Политика перехода индексов (файлов с логами) не правктикуется.
  * data_content - хост с БД не в кластере. Политика перехода индексов (файлов с логами) не правктикуется.
  * data_warm - хост кластера с теплыми данными
  * data_hot - хост кластера с горячими данными
  * data_cold - хост кластера с холодными данными
  * data_frozen - хост кластера с замороженными данными
  * ingest - принимает на себя поток данных и может производить их обработку (парсить, Json обогощать)
  * ml - machine learning
  * remote_cluster_client - для подключения между удаленными кластерами
  * transform - какой-то комбинатор для распределения нагрузки

### 8Введение     -00:10:39
Средством аггрегации логов, единой точка входа является Logstash. Данное
средство позволяет парсить и преобразовывать логи и складывать в
elasticsearch в удобном виде.
Средством визуализации логов является Kibana. Данное средство представляет
из себя веб-интерфейс и позволяет просматривать логи, строить графики на
основе данных из логов и многое другое.
Сбор логов осуществляется двумя путями:
● Приложение пишет логи напрямую в logstash (например, посредством tcp)
● Сбор производит Filebeat, который считывает данные логов из файлов.

### 9Logstash 

### 10Logstash      -00:12:00
Logstash в ELK-стэке сбора логов является
инструментом для приёма данных, их
преобразования и отправки в кластер БД
elasticsearch.
Конфигурирование Logstash осуществляется через
специальный конфигурационный файл.
Есть универсальный обработчик входных данных
grok. Данный обработчик позволяет парсить
входные данные и раскладывать приведённые
данные в виде ключ-значение для хранения в
elasticsearch.
* Logstash Можно заменть на Fluend какой-то

### 11Logstash      -00:12:18
Графа input конфигурационного
файла задаёт входные точки logstash.
Это может быть интерактивный обмен,
сетевое соединение, файл и т.д.
Также указывается тип входных
событий для их оптимальной
обработки, например syslog.
Взято с сайта: elastic.co

 -00:13:00 - плагины инпут [Docs Logstash Reference [8.1] » Input plugins](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)
   * плагин `beats` - собственная система инвентов Эластика. По ним может работать ФБ

### 12Logstash      -00:15:20
Графа ﬁlter отвечает за парсинг
входных данных и преобразованию
их в необходимую структуру.
Logstash имеет множество
стандартных парсеров входных
данных, но самым универсальным
является grok (но наименее
производительным).
В данной графе можно указать в
какие ключи складывать данные,
вставлять унарные операторы
в случае особых входных событий
и т.д.
Взято с сайта: elastic.co

### 13Logstash      -00:17:05
Графа output отвечает за конечную точку для отправки данных из Logstash.
Можно указать несколько конечных точек для параллельной отправки
данных. Например, файлы и Elasticsearch.
Также для каждой конечной точки существуют дополнительные гибкие
настройки, например добавление даты в название файла или применение
шаблона индексов в Elasticsearch к данным.
Взято с сайта: elastic.co

### 14Kibana

### 15Kibana     -00:19:30
Kibana является эффективным средством
визуализации данных логов.
В Kibana можно производить вывод как
текстовых данных, так и производить
сортировку логов по определенным
значениям (например, уровням логирования).
Возможно проведение агрегации данных и их
визуализация в виде диаграм, графиков или
интерактивных карт.

### 16Kibana     -00:19:45
Базовая настройка Kibana довольно проста и заключается лишь в указании
интерфейса, на котором она доступна и конечных точек источников данных
в виде массива сетевых параметров узлов elasticsearch.
Пример настройки Kibana:
Взято с сайта: kb.objectrocket.com

### 17Kibana
Взято с сайта: digitalocean.com

### 18Kibana
Взято с сайта: balagetech.com

### 19Kibana
Взято с сайта: logz.io

### 20Filebeat

### 21Filebeat      -00:21:24
Filebeat является эффективным
средством сбора данных логирования из
файлов и отправки в logstash/elasticsearch.
В процессе сбора Filebeat может
“схлопывать” данные логов при их
последовательном повторе, хранить данные в
буфере до восстановления конечной точки
отправки, игнорировать определенные
события логирования.
Filebeat

### 22Filebeat      -00:23:30
Базовая конфигурация Filebeat довольно проста и заключается в
настройке источника данных и конечной точки в конфигурационном
файле.
Пример настройки чтения логов из /var/logs и отправки их в logstash:
Взято с сайта: elastic.co

-00:24:30 - [The Beats Famaly](https://www.elastic.co/beats/)
  * Filebeat
  * Metricbeat
  * Packetbeat
  * Winlogbeat
  * Auditbeat
  * Heartbeat
  * Functionbeat

-00:29:20 - у Filebeat есть недостатки. Альтернатива: [Fluent Bit](https://fluentbit.io/)

### 23Index Lifecycle Management      -00:29:55
(ILM)

### 24Index Lifecycle Management
Index Lifecycle Management (ILM) -
процесс перемещения индексов (данных)
между нодами в Hot-Warm архитектуре.
Данный процесс настраивается с
помощью API Elasticsearch.
Настройка процесса перемещения
индекса между узлами - называется
политикой.
В политике можно указать триггер
перемещения индекса по времени или
по размеру primary shard индекса на
ноде определённого типа.
Взято с сайта: elastic.co

### 25Index Lifecycle Management
Чтобы привязать индекс к
политике, необходимо указать
шаблон, в котором указано, к каким
индексам применяется данная
политика.
В шаблоне индексов вы можете
использовать wildcard и
распространить данный шаблон на
несколько или на все индексы.
Взято с сайта: elastic.co
Также в шаблоне необходимо
указать псевдоним (alias) для
индекса.

### 26Index Lifecycle Management
Привязка псевдонимов также
осуществляется посредством
API Elasticsearch.
Псевдонимы упрощают поиск
индексов между нодами кластера
и ограничивает операции записи
только на индексы hot узлов.
Взято с сайта: elastic.co

### 27Пример политики
hot-warm-cold

### 28Пример политики hot-warm-cold
На данном этапе устанавливаем
высокий приоритет индекса,
чтобы горячие индексы
восстанавливались раньше других
индексов (при перезагрузке узла).
Через 30 дней или 50 ГБ (в
зависимости от того, что наступит
раньше) индекс будет обновлен
(перенесён), и будет создан новый
индекс.
Новый индекс запустит политику
заново, а текущий индекс будет
перенесён в warm фазу.
Взято с сайта: elastic.co

### 29Пример политики hot-warm-cold
Когда индекс перейдёт в теплую фазу:
● сократится количество его шардов до 1
● установится приоритет индекса на
значение ниже hot (но больше, чем cold)
● произойдет миграция индекса на warm
узлы
● срок жизни в warm фазе составляет 23
дня
P.S.: min_age указывает сколько должно
минимально пройти времени с создания
индекса до перехода в какую-либо фазу
Взято с сайта: elastic.co

### 30Пример политики hot-warm-cold
При переходе в холодную фазу:
● индекс замораживается, то есть
становится недоступным, но хранится
для его возможного будущего
использования
● установится минимальный приоритет
восстановления индекса
● произойдет миграция индекса на cold
узлы
● срок жизни индекса в cold фазе
составляет 30 дней
Взято с сайта: elastic.co

### 31Пример политики hot-warm-cold
Данная фаза управляет удалением
индексов.
В текущей политике указано, что индекс
должен быть удален по истечении срока
его жизни 60 дней, в независимости от
его фазы.
Взято с сайта: elastic.co

### 32Настройка Elasticsearch

### 33Настройка elasticsearch
Указание типа узла происходит путём добавление конфигурации в
elasticsearch.yml (прочие настройки стандартны для кластера elasticsearch):
Взято с сайта: habr.com

### 34Настройка elasticsearch
Рекомендуемые конфигурации для построения hot-warm кластера:
● Hot узлы
○
○
○
16 CPU
32 GB RAM
256 SSD
● Warm узлы
○
○
○
8 CPU
16 GB RAM
1 Tb Hdd
Данная конфигурация позволяет обслуживать обработку следующих
потоков данных логирования:
● размер индексов: до 30 Gb
● количество индексов: до 35

-00:44:45 - Практическая часть. Работа с elasticsearch
-00:55:40 - [Grafana Loki ](https://grafana.com/docs/loki/latest/?pg=oss-loki&plcmt=quick-links)
-00:57:30 - [Vector by Datadog](https://vector.dev/)
-00:58:50 - у них на работе такой стэк: Vector agent --> Kafka --> Vector --> Loki --> Grafana


### 35Итоги

### 36Итоги
В данной лекции мы узнали:
● Что такое hot-warm архитектура
● Домены ответственности инструментов
Logstash/Kibana/Filebeat и их базовую настройку
● Как реализовать hot-warm архитектуру на основе ELK-стэка
● Рассмотрели пример политики реализации hot-warm
архитектуры
● Рассмотрели рекомендуемые требования hot-warm
elasticsearch кластера в составе ELK

### 37Домашнее задание    -01:01:40
Давайте посмотрим ваше домашнее задание.
● Вопросы по домашней работе задавайте в чате мессенджера Slack.
● Задачи можно сдавать по частям.
● Зачёт по домашней работе проставляется после того, как приняты
все задачи.

### 38Задавайте вопросы и
пишите отзыв о лекции!
Сергей Бывшев
Сергей Бывшев



