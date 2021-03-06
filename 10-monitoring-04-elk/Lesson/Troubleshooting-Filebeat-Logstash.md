## Ход работ по связыванию Filebeat и Logstash

### 1. Описание ситуации

1. Не поступают данные (логи) контейнеров в Logastash
2. Logstash может передавать данные в Elasticksearch
3. В Kibana от Elastic индекс поступает

### 2. Описание связки Filebeat и Logstash

1. Порт Логстэш, принимающий от ФБ - 9600, судя по его логам

```
logstash  | [2022-04-15T18:01:17,438][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600, :ssl_enabled=>false}
```
```
logstash  | [2022-04-15T18:01:29,270][INFO ][logstash.inputs.tcp      ][main][2a645a01954fb75b8efb5b4ed2431d900cd929dd8bcebdc13e0d7840b4037b6c] Starting tcp input listener {:address=>"0.0.0.0:5046", :ssl_e>
```
Вывод: правильный `tcp input listener` порт - 5046

2. Настройка входящего фильтра от ФБ для JSON
* Фиксируется ошибка:
```

```
* Вот такой файл приходит от Filebeat в Logstash при нормальной передачи логов. Это необработанные данные. Бинарные. Т.к. стоит входной параматр tcp, а не beats
```json
{
  "_index": "logstash-2022.04.19",
  "_type": "_doc",
  "_id": "E8cVQIAB4iChi9AGHMV2",
  "_version": 1,
  "_score": 1,
  "_source": {
    "port": 33088,
    "message": "\\xDBl\\xE0\\xAC­嚇\\a\\xF0p3\\x8Fo3\\xB8oxj玩\\x9D\\x86'\\x90\\u0018\\x9E@\\xA2#\\x81\\xC4+\\xF3\\u0005\\xD6|\\b\\f?\\x85\\xC9\\xF0S\\x98\\f?\\x85\\xE9`Oa\\\"`z\\xD6\\xED\\u0003\\xD5.Xw\\b\\u0013X\\x87\\x8E\\xEB[\\xE5n\\u0004\\\"\\xE4\\u0019\\x87\\xD30H\\xCA\\u0014Ex\\x949~\\xF5\\xDC \\u0011\\xF2\\xB4V\\x8A|\\x9A\\xE4\\xBBD\\xC9\\xEB\\u000E\\xA6\\xA0V\\xA5@#(\\x9BnYj\\x91\\xEDsѾ\\xB8\\xD4\\xD4<\\xAB7/\\ew\\xF7\\xD1y\\xBE\\xDA\\xF9*\\rd\\u000F\\u0000\\xB85\\u0012\\xA0\\xFF\\u001C\\xE5m\\x8B+M\\xD8{ͽ\\xAFP\\xE1\\xEC\\x83\\v8\\xF3LY\\xCD\\u0005\\xD0p<\\xFD\\x9C\\xC0\\x99[\\xCAj*@\\u0004\\u0013,\\x81K=<\\xC1\\x99R\\xB2\\xAAE6\\xBE\\u000F\\u0004\\xC8\\u001A\\u0004j\\u0000\\xC0\\xB5&\\u0004\\xE8\\u001A\\\"*\\x84H\\xB1\\u0014ti\\xA2\\xC39\\u00040X\\x9D\\b\\xC9",
    "@version": "1",
    "@timestamp": "2022-04-19T04:29:26.845Z",
    "host": "filebeat.elk_elastic",
    "tags": [
      "_jsonparsefailure"
    ]
  },
  "fields": {
    "@timestamp": [
      "2022-04-19T04:29:26.845Z"
    ],
    "port": [
      33088
    ],
    "tags.keyword": [
      "_jsonparsefailure"
    ],
    "@version": [
      "1"
    ],
    "host": [
      "filebeat.elk_elastic"
    ],
    "host.keyword": [
      "filebeat.elk_elastic"
    ],
    "message": [
      "\\xDBl\\xE0\\xAC­嚇\\a\\xF0p3\\x8Fo3\\xB8oxj玩\\x9D\\x86'\\x90\\u0018\\x9E@\\xA2#\\x81\\xC4+\\xF3\\u0005\\xD6|\\b\\f?\\x85\\xC9\\xF0S\\x98\\f?\\x85\\xE9`Oa\\\"`z\\xD6\\xED\\u0003\\xD5.Xw\\b\\u0013X\\x87\\x8E\\xEB[\\xE5n\\u0004\\\"\\xE4\\u0019\\x87\\xD30H\\xCA\\u0014Ex\\x949~\\xF5\\xDC \\u0011\\xF2\\xB4V\\x8A|\\x9A\\xE4\\xBBD\\xC9\\xEB\\u000E\\xA6\\xA0V\\xA5@#(\\x9BnYj\\x91\\xEDsѾ\\xB8\\xD4\\xD4<\\xAB7/\\ew\\xF7\\xD1y\\xBE\\xDA\\xF9*\\rd\\u000F\\u0000\\xB85\\u0012\\xA0\\xFF\\u001C\\xE5m\\x8B+M\\xD8{ͽ\\xAFP\\xE1\\xEC\\x83\\v8\\xF3LY\\xCD\\u0005\\xD0p<\\xFD\\x9C\\xC0\\x99[\\xCAj*@\\u0004\\u0013,\\x81K=<\\xC1\\x99R\\xB2\\xAAE6\\xBE\\u000F\\u0004\\xC8\\u001A\\u0004j\\u0000\\xC0\\xB5&\\u0004\\xE8\\u001A\\\"*\\x84H\\xB1\\u0014ti\\xA2\\xC39\\u00040X\\x9D\\b\\xC9"
    ],
    "tags": [
      "_jsonparsefailure"
    ]
  }
}
```
```
{
  "_index": "logstash-2022.04.20",
  "_type": "_doc",
  "_id": "rKw9RYABmp83w62qTgaY",
  "_version": 1,
  "_score": 1,
  "_source": {
    "log": {
      "offset": 476,
      "file": {
        "path": "/var/lib/docker/containers/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c-json.log"
      }
    },
    "agent": {
      "hostname": "d06ab0375ab7",
      "id": "6a11d0a9-d837-46fc-bf64-43a0e3db06b3",
      "type": "filebeat",
      "version": "7.16.2",
      "name": "d06ab0375ab7",
      "ephemeral_id": "bec8503a-e548-40a0-9ed1-a3e832f6f971"
    },
    "host": {
      "name": "d06ab0375ab7"
    },
    "ecs": {
      "version": "1.12.0"
    },
    "message": "ERROR:root:OH NO!!!!!!",
    "@timestamp": "2022-04-20T04:28:40.215Z",
    "input": {
      "type": "container"
    },
    "@version": "1",
    "container": {
      "image": {
        "name": "library/python:3.9-alpine"
      },
      "labels": {
        "com_docker_compose_container-number": "1",
        "com_docker_compose_depends_on": "",
        "com_docker_compose_project_config_files": "/root/learning-monitoring/ELK/docker-compose.yml",
        "com_docker_compose_project_working_dir": "/root/learning-monitoring/ELK",
        "com_docker_compose_service": "some_application",
        "com_docker_compose_oneoff": "False",
        "com_docker_compose_project": "elk",
        "com_docker_compose_version": "2.2.2",
        "com_docker_compose_config-hash": "29129dac1567d42191497fbdaa4e14f37ab58c37fe176c4148a36f16d7a6ea4a",
        "com_docker_compose_image": "sha256:c55bae5557c5c540d01302d5feb93a89035a224bb9d14346ec47ec2d5ebc3f3d"
      },
      "name": "some_app",
      "id": "7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c"
    },
    "stream": "stderr",
    "tags": [
      "_jsonparsefailure",
      "beats_input_codec_json_applied"
    ]
  },
  "fields": {
    "agent.version.keyword": [
      "7.16.2"
    ],
    "container.labels.com_docker_compose_project_working_dir.keyword": [
      "/root/learning-monitoring/ELK"
    ],
    "container.labels.com_docker_compose_project_working_dir": [
      "/root/learning-monitoring/ELK"
    ],
    "host.name.keyword": [
      "d06ab0375ab7"
    ],
    "container.labels.com_docker_compose_service": [
      "some_application"
    ],
    "container.id": [
      "7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c"
    ],
    "agent.hostname.keyword": [
      "d06ab0375ab7"
    ],
    "ecs.version.keyword": [
      "1.12.0"
    ],
    "container.name": [
      "some_app"
    ],
    "container.image.name": [
      "library/python:3.9-alpine"
    ],
    "container.labels.com_docker_compose_image": [
      "sha256:c55bae5557c5c540d01302d5feb93a89035a224bb9d14346ec47ec2d5ebc3f3d"
    ],
    "agent.name": [
      "d06ab0375ab7"
    ],
    "host.name": [
      "d06ab0375ab7"
    ],
    "container.name.keyword": [
      "some_app"
    ],
    "agent.id.keyword": [
      "6a11d0a9-d837-46fc-bf64-43a0e3db06b3"
    ],
    "input.type": [
      "container"
    ],
    "log.offset": [
      476
    ],
    "agent.hostname": [
      "d06ab0375ab7"
    ],
    "tags": [
      "_jsonparsefailure",
      "beats_input_codec_json_applied"
    ],
    "container.labels.com_docker_compose_project": [
      "elk"
    ],
    "container.labels.com_docker_compose_image.keyword": [
      "sha256:c55bae5557c5c540d01302d5feb93a89035a224bb9d14346ec47ec2d5ebc3f3d"
    ],
    "agent.id": [
      "6a11d0a9-d837-46fc-bf64-43a0e3db06b3"
    ],
    "ecs.version": [
      "1.12.0"
    ],
    "container.labels.com_docker_compose_project_config_files.keyword": [
      "/root/learning-monitoring/ELK/docker-compose.yml"
    ],
    "container.labels.com_docker_compose_version.keyword": [
      "2.2.2"
    ],
    "agent.version": [
      "7.16.2"
    ],
    "container.labels.com_docker_compose_depends_on": [
      ""
    ],
    "container.labels.com_docker_compose_container-number.keyword": [
      "1"
    ],
    "stream.keyword": [
      "stderr"
    ],
    "container.labels.com_docker_compose_config-hash": [
      "29129dac1567d42191497fbdaa4e14f37ab58c37fe176c4148a36f16d7a6ea4a"
    ],
    "input.type.keyword": [
      "container"
    ],
    "tags.keyword": [
      "_jsonparsefailure",
      "beats_input_codec_json_applied"
    ],
    "container.labels.com_docker_compose_depends_on.keyword": [
      ""
    ],
    "container.labels.com_docker_compose_service.keyword": [
      "some_application"
    ],
    "agent.type": [
      "filebeat"
    ],
    "stream": [
      "stderr"
    ],
    "container.labels.com_docker_compose_oneoff.keyword": [
      "False"
    ],
    "@version": [
      "1"
    ],
    "container.labels.com_docker_compose_config-hash.keyword": [
      "29129dac1567d42191497fbdaa4e14f37ab58c37fe176c4148a36f16d7a6ea4a"
    ],
    "container.image.name.keyword": [
      "library/python:3.9-alpine"
    ],
    "log.file.path.keyword": [
      "/var/lib/docker/containers/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c-json.log"
    ],
    "agent.type.keyword": [
      "filebeat"
    ],
    "agent.ephemeral_id.keyword": [
      "bec8503a-e548-40a0-9ed1-a3e832f6f971"
    ],
    "container.labels.com_docker_compose_project.keyword": [
      "elk"
    ],
    "agent.name.keyword": [
      "d06ab0375ab7"
    ],
    "message": [
      "ERROR:root:OH NO!!!!!!"
    ],
    "container.labels.com_docker_compose_version": [
      "2.2.2"
    ],
    "container.labels.com_docker_compose_oneoff": [
      "False"
    ],
    "@timestamp": [
      "2022-04-20T04:28:40.215Z"
    ],
    "log.file.path": [
      "/var/lib/docker/containers/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c/7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c-json.log"
    ],
    "agent.ephemeral_id": [
      "bec8503a-e548-40a0-9ed1-a3e832f6f971"
    ],
    "container.labels.com_docker_compose_container-number": [
      "1"
    ],
    "container.id.keyword": [
      "7de09c2ef6b59478883fc6568c99d7699326e270bcd99cc471df3eb458b4c57c"
    ],
    "container.labels.com_docker_compose_project_config_files": [
      "/root/learning-monitoring/ELK/docker-compose.yml"
    ]
  }
}
```

3. Проверка нод стэка. [Источник информаци - Run with Docker Compose](https://www.elastic.co/guide/en/elastic-stack-get-started/7.16/get-started-docker.html)
```
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec es-hot curl -X GET "localhost:9200/_cat/nodes?v&pretty"
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role  master name
172.24.0.3           71          76   3    0.30    0.23     0.25 cdfhimrstw *      es-hot
172.24.0.2           74          76   3    0.30    0.23     0.25 cdfhimrstw -      es-warm
root@server1:~/learning-monitoring/ELK/pinger# 
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec es-warm curl -X GET "localhost:9200/_cat/nodes?v&pretty"
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role  master name
172.24.0.3           33          76   3    0.24    0.22     0.25 cdfhimrstw *      es-hot
172.24.0.2           37          76   3    0.24    0.22     0.25 cdfhimrstw -      es-warm

```

4. Убеждаемся, что Elasticsearch запущен и работает.
Чтобы проверить, запущен ли демон Elasticsearch, попробуйте отправить HTTP-запрос GET на порт 9200.
```
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec es-warm curl http://localhost:9200
{
  "name" : "es-warm",
  "cluster_name" : "es-docker-cluster",
  "cluster_uuid" : "V6JcOag3SEizV7t4nYi1Cg",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```
```
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec es-hot curl http://localhost:9200
{
  "name" : "es-hot",
  "cluster_name" : "es-docker-cluster",
  "cluster_uuid" : "V6JcOag3SEizV7t4nYi1Cg",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```

```
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec kibana curl http://es-hot:9200
{
  "name" : "es-hot",
  "cluster_name" : "es-docker-cluster",
  "cluster_uuid" : "V6JcOag3SEizV7t4nYi1Cg",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
```
root@server1:~/learning-monitoring/ELK/pinger# docker-compose exec kibana curl http://es-warm:9200
{
  "name" : "es-warm",
  "cluster_name" : "es-docker-cluster",
  "cluster_uuid" : "V6JcOag3SEizV7t4nYi1Cg",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```
5. Проверка того, что ФБ генерирует сообщения с логами.
* Configure Filebeat
[Configure Filebeat](https://www.elastic.co/guide/en/beats/filebeat/7.16/configuring-howto-filebeat.html)

[Filebeat quick start: installation and configuration](https://www.elastic.co/guide/en/beats/filebeat/7.16/filebeat-installation-configuration.html)
* Директория с логами указана в файле `docker-compose.yml`

```
filebeat.inputs:
  - type: container
    paths:
      - '/var/lib/docker/containers/*/*.log'
```
* Содержимое директории `/var/lib/docker/containers/`
```
[root@73c225c74f4c containers]# ls -lha
total 104K
drwx--x--- 26 root root 4.0K Apr 15 17:59 .
drwx--x--- 13 root root 4.0K Apr 15 17:06 ..
drwx--x---  4 root root 4.0K Apr 15 17:59 03b540905831c8e2ec5bb9652571d702bf5e5c0268092dc4dad2ea815727a419
drwx--x---  4 root root 4.0K Apr 15 17:06 0402e79fb4421a5c1b76e3f70f03c96daecd7f252e8631bb0f560b8ef2853e88
drwx--x---  4 root root 4.0K Apr 15 17:59 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12
drwx--x---  4 root root 4.0K Apr 15 17:06 0eabb98f5d269e4191b92baf6a32b165056d7578f7237a142b98038f50dafa8a
drwx--x---  3 root root 4.0K Apr 15 17:06 1157959a0572931138c0500b3c4c6b743553b9ac9d0b7b8e4ce13a1709e58146
drwx--x---  4 root root 4.0K Apr 15 17:06 1368b340daa9fa9bb50809eb8623e1a1b6e4198ed831d92d1b7414039d400146
drwx--x---  4 root root 4.0K Apr 15 17:06 19eb840a18165cf8d1b3a2a2d6efb515ad58aa42a8fb572db171163fba807db6
drwx--x---  4 root root 4.0K Apr 15 17:06 2460b82403b84c52dad2ab17ec903ea858cb7c500c11ee4dbe5a6d71942b6401
drwx--x---  4 root root 4.0K Apr 15 17:06 362677b4f094da89a8fcb49969cd05d395e1609acebe90d41dcef5671aa0d928
drwx--x---  3 root root 4.0K Apr 15 17:06 3d4224a38b64f7a2b18cdf91bb29e15522db600d27a45ca759db701083282ffc
drwx--x---  4 root root 4.0K Apr 15 17:59 3dd9f0293ec46a270618a8da011597aa55d08dca98309e6246367733f41809b3
drwx--x---  4 root root 4.0K Apr 15 17:06 43d7298dd0e3ca47d9ea63091c5891e402e3a738e727fef2f6688e444da44cc4
drwx--x---  4 root root 4.0K Apr 15 17:06 49db9913bea21726041f75e276a23e035260b2f935960b0092f97a81bc24a8d7
drwx--x---  4 root root 4.0K Apr 15 17:06 4cb472998e00ae8080f8d78cdbbb672ab6d9128d874a4dcd8f05ff61014c0d4b
drwx--x---  4 root root 4.0K Apr 15 17:06 645f020fc5b892e4e8a2dc4bc7c284728e5c21fe74295263a39de883b576c464
drwx--x---  4 root root 4.0K Apr 15 17:06 67bf8c81f414aa4d94a98b2a8c95ea8a82cddab1453104e1ba7b3aad95935ece
drwx--x---  4 root root 4.0K Apr 15 17:59 73c225c74f4c6d73b6582441b06e0e88c37ef0ab5fe6232ce0756027f39e5eb2
drwx--x---  4 root root 4.0K Apr 15 17:59 9243fc84cfae7381d14b52bc47fbaf50bc87bbeead1084c0e5ef8bfa45990700
drwx--x---  3 root root 4.0K Apr 15 17:06 9ebe3103b2926ce0d281a16f63ba88802c667ce9c8f66fc1d24f109bf8ccd2fa
drwx--x---  4 root root 4.0K Apr 15 17:06 b5e8565a29afd11a26554ee62ac636469239ecb7ab785bf05296183b3d1fd235
drwx--x---  4 root root 4.0K Apr 15 17:06 c25089291551360769742cb33de67d257bd7026e8a0fe2da25c1cbcbad351147
drwx--x---  4 root root 4.0K Apr 15 17:06 d64c2e97d86aa1c095e059ff2a2140094052559edbf45410a1bb818ffbaf619b
drwx--x---  4 root root 4.0K Apr 15 17:06 e2521ac975768239fb51f5d1198a8f37bae9286184cae8e837fccd5ea9237805
drwx--x---  4 root root 4.0K Apr 15 17:59 e2ce61ea8cbe68f6524dd7bdb6dbf8b1b444e0db07ba45db8fcb024400708e55

```

```
[root@73c225c74f4c containers]# cd 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12/
[root@73c225c74f4c 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12]# 
[root@73c225c74f4c 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12]# ls -lha
total 136K
drwx--x---  4 root root 4.0K Apr 15 17:59 .
drwx--x--- 26 root root 4.0K Apr 15 17:59 ..
-rw-r-----  1 root root  86K Apr 16 03:51 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12-json.log
drwx------  2 root root 4.0K Apr 15 17:59 checkpoints
-rw-------  1 root root 5.8K Apr 15 17:59 config.v2.json
-rw-r--r--  1 root root 1.8K Apr 15 17:59 hostconfig.json
-rw-r--r--  1 root root   13 Apr 15 17:59 hostname
-rw-r--r--  1 root root  174 Apr 15 17:59 hosts
drwx--x---  2 root root 4.0K Apr 15 17:59 mounts
-rw-r--r--  1 root root   70 Apr 15 17:59 resolv.conf
-rw-r--r--  1 root root   71 Apr 15 17:59 resolv.conf.hash

```

```
[root@73c225c74f4c 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12]# cat 04b1193043eb7c8fac285c9ed8b784de641180cbfcff4efd954ca44bf846da12-json.log 
{"log":"si_signo 4, si_code: 2, si_errno: 0, address: 0x7ff77902a2f8, library: /usr/share/elasticsearch/modules/x-pack-ml/platform/linux-x86_64/bin/../lib/libMlCore.so, base: 0x7ff778f06000, normalized address: 0x1242f8\n","stream":"stderr","time":"2022-04-15T17:59:48.605082427Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T17:59:52,295Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"version[7.16.2], pid[8], build[default/docker/2b937c44140b6559905130a8650c64dbd0879cfb/2021-12-18T19:42:46.604893745Z], OS[Linux/5.4.0-80-generic/amd64], JVM[Eclipse Adoptium/OpenJDK 64-Bit Server VM/17.0.1/17.0.1+12]\" }\n","stream":"stdout","time":"2022-04-15T17:59:52.314526936Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T17:59:52,320Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"JVM home [/usr/share/elasticsearch/jdk], using bundled JDK [true]\" }\n","stream":"stdout","time":"2022-04-15T17:59:52.321897998Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T17:59:52,322Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"JVM arguments [-Xshare:auto, -Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -XX:+ShowCodeDetailsInExceptionMessages, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dio.netty.allocator.numDirectArenas=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Dlog4j2.formatMsgNoLookups=true, -Djava.locale.providers=SPI,COMPAT, --add-opens=java.base/java.io=ALL-UNNAMED, -XX:+UseG1GC, -Djava.io.tmpdir=/tmp/elasticsearch-2176962129824025582, -XX:+HeapDumpOnOutOfMemoryError, -XX:+ExitOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m, -Des.cgroups.hierarchy.override=/, -Xms512m, -Xmx512m, -XX:MaxDirectMemorySize=268435456, -XX:G1HeapRegionSize=4m, -XX:InitiatingHeapOccupancyPercent=30, -XX:G1ReservePercent=15, -Des.path.home=/usr/share/elasticsearch, -Des.path.conf=/usr/share/elasticsearch/config, -Des.distribution.flavor=default, -Des.distribution.type=docker, -Des.bundled_jdk=true]\" }\n","stream":"stdout","time":"2022-04-15T17:59:52.32344326Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,609Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [aggs-matrix-stats]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.620656671Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,620Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [analysis-common]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.621398771Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,621Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [constant-keyword]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.621792225Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,621Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [frozen-indices]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.622277876Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,622Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [ingest-common]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.62265598Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,622Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [ingest-geoip]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.623122252Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,623Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [ingest-user-agent]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.623552103Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,623Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [kibana]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.623935907Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,623Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [lang-expression]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.62441755Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,624Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [lang-mustache]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.625053387Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,624Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [lang-painless]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.63723343Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,637Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [legacy-geo]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.637669744Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,637Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [mapper-extras]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.638136997Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,638Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [mapper-version]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.638572783Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,638Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [parent-join]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.664664287Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,664Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [percolator]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.665243677Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,665Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [rank-eval]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.665647013Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,665Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [reindex]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.665987525Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,665Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [repositories-metering-api]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.66646951Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,666Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [repository-encrypted]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.66695602Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,666Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [repository-url]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.667333364Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,667Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [runtime-fields-common]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.667819365Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,667Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [search-business-rules]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.668227054Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,670Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [searchable-snapshots]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.671297604Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,671Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [snapshot-repo-test-kit]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.671630778Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,671Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [spatial]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.672116496Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,672Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [transform]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.67262431Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,672Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [transport-netty4]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.673008965Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,672Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [unsigned-long]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.673426246Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,673Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [vector-tile]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.673893546Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,673Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [vectors]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.674281078Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,674Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [wildcard]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.674687428Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,674Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-aggregate-metric]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.675137267Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,675Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-analytics]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.675561465Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,675Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-async]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.676100757Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,700Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-async-search]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.701631272Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,701Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-autoscaling]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.701966002Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,701Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-ccr]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.702340992Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,702Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-core]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.702847409Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,702Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-data-streams]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.70321273Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,703Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-deprecation]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.70358361Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,703Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-enrich]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.704049926Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,703Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-eql]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.706455624Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,706Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-fleet]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.70742587Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,707Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-graph]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.707916181Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,707Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-identity-provider]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.708750696Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,708Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-ilm]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.709133266Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,709Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-logstash]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.709794935Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,709Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-ml]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.710832894Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,710Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-monitoring]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.711210869Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,711Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-ql]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.711693614Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,711Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-rollup]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.722561349Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,722Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-security]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.723277995Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,723Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-shutdown]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.723632303Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,723Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-sql]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.724006485Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,723Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-stack]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.730071116Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,729Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-text-structure]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.730473207Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,730Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-voting-only-node]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.74964084Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,749Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"loaded module [x-pack-watcher]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.750120216Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,751Z\", \"level\": \"INFO\", \"component\": \"o.e.p.PluginsService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"no plugins loaded\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.752465345Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,974Z\", \"level\": \"INFO\", \"component\": \"o.e.e.NodeEnvironment\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"using [1] data paths, mounts [[/usr/share/elasticsearch/data (/dev/mapper/vgvagrant-root)]], net usable_space [42.4gb], net total_space [61.3gb], types [ext4]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.986352398Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:05,992Z\", \"level\": \"INFO\", \"component\": \"o.e.e.NodeEnvironment\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"heap size [512mb], compressed ordinary object pointers [true]\" }\n","stream":"stdout","time":"2022-04-15T18:00:05.993802366Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:06,350Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"node name [es-warm], node ID [uZM41So1RiSoKtHUGOwZUQ], cluster name [es-docker-cluster], roles [transform, data_frozen, master, remote_cluster_client, data, data_content, data_hot, data_warm, data_cold, ingest]\" }\n","stream":"stdout","time":"2022-04-15T18:00:06.361212246Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:33,593Z\", \"level\": \"INFO\", \"component\": \"o.e.x.s.a.s.FileRolesStore\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"parsed [0] roles from file [/usr/share/elasticsearch/config/roles.yml]\" }\n","stream":"stdout","time":"2022-04-15T18:00:33.594352802Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:36,489Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.ConfigDatabases\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"initialized default databases [[GeoLite2-Country.mmdb, GeoLite2-City.mmdb, GeoLite2-ASN.mmdb]], config databases [[]] and watching [/usr/share/elasticsearch/config/ingest-geoip] for changes\" }\n","stream":"stdout","time":"2022-04-15T18:00:36.489857213Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:36,491Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"initialized database registry, using geoip-databases directory [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ]\" }\n","stream":"stdout","time":"2022-04-15T18:00:36.492658679Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:38,782Z\", \"level\": \"INFO\", \"component\": \"o.e.t.NettyAllocator\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"creating NettyAllocator with the following configs: [name=unpooled, suggested_max_allocation_size=1mb, factors={es.unsafe.use_unpooled_allocator=null, g1gc_enabled=true, g1gc_region_size=4mb, heap_size=512mb}]\" }\n","stream":"stdout","time":"2022-04-15T18:00:38.782874088Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:39,042Z\", \"level\": \"INFO\", \"component\": \"o.e.d.DiscoveryModule\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"using discovery type [zen] and seed hosts providers [settings]\" }\n","stream":"stdout","time":"2022-04-15T18:00:39.043701618Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:40,905Z\", \"level\": \"INFO\", \"component\": \"o.e.g.DanglingIndicesState\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"gateway.auto_import_dangling_indices is disabled, dangling indices will not be automatically detected or imported and must be managed manually\" }\n","stream":"stdout","time":"2022-04-15T18:00:40.906112715Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:43,132Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"initialized\" }\n","stream":"stdout","time":"2022-04-15T18:00:43.133345498Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:43,133Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"starting ...\" }\n","stream":"stdout","time":"2022-04-15T18:00:43.134183244Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:43,166Z\", \"level\": \"INFO\", \"component\": \"o.e.x.s.c.f.PersistentCache\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"persistent cache index loaded\" }\n","stream":"stdout","time":"2022-04-15T18:00:43.168777089Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:43,170Z\", \"level\": \"INFO\", \"component\": \"o.e.x.d.l.DeprecationIndexingComponent\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"deprecation component started\" }\n","stream":"stdout","time":"2022-04-15T18:00:43.171305443Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:43,431Z\", \"level\": \"INFO\", \"component\": \"o.e.t.TransportService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"publish_address {172.24.0.2:9300}, bound_addresses {0.0.0.0:9300}\" }\n","stream":"stdout","time":"2022-04-15T18:00:43.437505621Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:45,246Z\", \"level\": \"INFO\", \"component\": \"o.e.b.BootstrapChecks\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"bound or publishing to a non-loopback address, enforcing bootstrap checks\" }\n","stream":"stdout","time":"2022-04-15T18:00:45.247125496Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:45,368Z\", \"level\": \"INFO\", \"component\": \"o.e.c.c.Coordinator\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"cluster UUID [V6JcOag3SEizV7t4nYi1Cg]\" }\n","stream":"stdout","time":"2022-04-15T18:00:45.369547784Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:49,117Z\", \"level\": \"INFO\", \"component\": \"o.e.c.s.ClusterApplierService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"master node changed {previous [], current [{es-hot}{Y7cFLtZgSqqxpwt1RgnI5Q}{H_iCEtJ0QQa5wDXg5jq2IA}{172.24.0.3}{172.24.0.3:9300}{cdfhimrstw}]}, added {{es-hot}{Y7cFLtZgSqqxpwt1RgnI5Q}{H_iCEtJ0QQa5wDXg5jq2IA}{172.24.0.3}{172.24.0.3:9300}{cdfhimrstw}}, term: 7, version: 239, reason: ApplyCommitRequest{term=7, version=239, sourceNode={es-hot}{Y7cFLtZgSqqxpwt1RgnI5Q}{H_iCEtJ0QQa5wDXg5jq2IA}{172.24.0.3}{172.24.0.3:9300}{cdfhimrstw}{xpack.installed=true, transform.node=true}}\" }\n","stream":"stdout","time":"2022-04-15T18:00:49.118408706Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:49,173Z\", \"level\": \"INFO\", \"component\": \"o.e.h.AbstractHttpServerTransport\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"publish_address {172.24.0.2:9200}, bound_addresses {0.0.0.0:9200}\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:49.173895862Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:49,174Z\", \"level\": \"INFO\", \"component\": \"o.e.n.Node\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"started\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:49.174561908Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:50,334Z\", \"level\": \"INFO\", \"component\": \"o.e.l.LicenseService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"license [b7612816-8461-4042-9171-14cddf5ce7b2] mode [basic] - valid\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:50.336440016Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:50,347Z\", \"level\": \"INFO\", \"component\": \"o.e.x.s.s.SecurityStatusChangeListener\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"Active license is now [BASIC]; Security is disabled\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:50.348930926Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:50,349Z\", \"level\": \"WARN\", \"component\": \"o.e.x.s.s.SecurityStatusChangeListener\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"Elasticsearch built-in security features are not enabled. Without authentication, your cluster could be accessible to anyone. See https://www.elastic.co/guide/en/elasticsearch/reference/7.16/security-minimal-setup.html to enable security.\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:50.349790421Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:00:50,361Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.x.s.s.SecurityStatusChangeListener\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"The default behavior of disabling security on basic licenses is deprecated. In a later version of Elasticsearch, the value of [xpack.security.enabled] will default to \\\"true\\\" , regardless of the license level. See https://www.elastic.co/guide/en/elasticsearch/reference/7.16/security-minimal-setup.html to enable security, or explicitly disable security by setting [xpack.security.enabled] to false in elasticsearch.yml\", \"key\": \"security_implicitly_disabled\", \"category\": \"security\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:50.364747825Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:51,783Z\", \"level\": \"INFO\", \"component\": \"o.e.x.s.a.TokenService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"refresh keys\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:51.783992937Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:52,201Z\", \"level\": \"INFO\", \"component\": \"o.e.x.s.a.TokenService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"refreshed keys\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:52.202037296Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:54,830Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"downloading geoip database [GeoLite2-Country.mmdb] to [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-Country.mmdb.tmp.gz]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:54.831445432Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:54,877Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"downloading geoip database [GeoLite2-City.mmdb] to [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-City.mmdb.tmp.gz]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:54.878166129Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:54,895Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"downloading geoip database [GeoLite2-ASN.mmdb] to [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-ASN.mmdb.tmp.gz]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:54.896358709Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:56,641Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"successfully reloaded changed geoip database file [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-Country.mmdb]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:56.642715964Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:00:57,576Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"successfully reloaded changed geoip database file [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-ASN.mmdb]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:00:57.577996173Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:01:03,171Z\", \"level\": \"INFO\", \"component\": \"o.e.i.g.DatabaseNodeService\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"successfully reloaded changed geoip database file [/tmp/elasticsearch-2176962129824025582/geoip-databases/uZM41So1RiSoKtHUGOwZUQ/GeoLite2-City.mmdb]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:01:03.172756623Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:01:05,336Z\", \"level\": \"INFO\", \"component\": \"o.e.t.LoggingTaskListener\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"233 finished with response BulkByScrollResponse[took=1.8s,timed_out=false,sliceId=null,updated=17,created=0,deleted=0,batches=1,versionConflicts=0,noops=0,retries=0,throttledUntil=0s,bulk_failures=[],search_failures=[]]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:01:05.337947994Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-15T18:01:08,359Z\", \"level\": \"INFO\", \"component\": \"o.e.t.LoggingTaskListener\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"234 finished with response BulkByScrollResponse[took=4.9s,timed_out=false,sliceId=null,updated=340,created=0,deleted=0,batches=1,versionConflicts=0,noops=0,retries=0,throttledUntil=0s,bulk_failures=[],search_failures=[]]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:01:08.360620745Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:29:09,000Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"840127d6-e914-4f28-ad11-a43b85b2fd2b\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:29:09.003022297Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:29:38,935Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"5de107dc-c37a-4109-9f23-c2cb4cfc437f\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:29:38.937241063Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:30:08,926Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"d8faf08c-b8f6-40c2-a7ea-e1a520e13fc2\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:30:08.927432565Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:30:38,929Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"07b10231-9169-493e-a8e6-bf8db7e39425\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:30:38.931270833Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:31:08,928Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"85ce64ab-7190-4b66-8b5a-37033bfd0af2\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:31:08.929832705Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:31:38,931Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"dd387743-fc65-4594-bfcc-f41bfa26d67a\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:31:38.93255058Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:32:08,923Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"accd1fc2-af34-42c9-b923-9c7985e15eb1\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:32:08.925158065Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:32:38,918Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"4b1251ee-a0ea-44ca-848d-f7a2192f9333\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:32:38.919491175Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:33:08,924Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"45aaf0bf-72a6-4b96-aa9b-06cd9c4bcd2d\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:33:08.925565216Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:33:38,926Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"65854d72-f913-4446-981d-7975fa606c48\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:33:38.92812825Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:34:08,938Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"6fe3df98-77bb-4e02-b823-d238adf0b3b6\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:34:08.939468747Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:34:38,923Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"9fae129b-3e1e-4e41-97f8-6357f2d2d8aa\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:34:38.924930195Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:35:08,942Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"52c79587-2115-490d-af07-21c7cf4b9586\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:35:08.943983864Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:35:38,931Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"f7a64e20-7adb-4b6c-89b5-90bf90e22513\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:35:38.932506946Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:36:08,926Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"560aa193-d260-4a25-a1ab-63cb9eb714d5\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:36:08.927506409Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:36:38,939Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"87989d91-3424-4487-8cb5-17d7aa5b9dff\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:36:38.941409031Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:37:08,923Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"cf130473-1a5f-4d32-9359-0e28cb81a1e7\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:37:08.924514638Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:37:38,929Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"93df5f03-a5f3-417b-853d-d75b8eb6a76a\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:37:38.930010574Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:38:08,925Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"39ea21e9-03e2-4fd7-b7ce-b7bf7d487d7d\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:38:08.926505257Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:38:38,928Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"23f02857-73f0-4585-b72e-dda1845c97a5\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:38:38.929073216Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:39:08,960Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"c78e8bf7-cea4-4f5c-b049-8ca18d600e08\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:39:08.961956076Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:39:38,927Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"f5aeed7c-b05b-42fd-bb5e-d609d8ce6dbd\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:39:38.92824263Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:40:08,930Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"5ba66836-078e-4d38-b340-dc60cf2f7d76\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:40:08.931565539Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:40:38,922Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"491baaf6-587d-4df8-8c2a-ffca3ae7a6cd\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:40:38.923204692Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:41:08,928Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"e4e891bc-12e8-4113-87d3-c5e53dc372ff\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:41:08.929657124Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:41:38,925Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"0c78b5a6-6e78-4506-8526-718c94dfe51c\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:41:38.92685315Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:42:08,926Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"77818b8d-c320-4401-a216-ab0cf041a2b6\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:42:08.927978714Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:42:38,927Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"847e47fb-7506-48c3-9942-19f0f12a5260\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:42:38.92869137Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:43:08,928Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"3b2f945d-bc13-4a7d-b3f2-248f82567d78\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:43:08.92943104Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:43:38,925Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"fa585942-2f59-403f-a4e5-ede5f785115f\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:43:38.926176095Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:44:08,927Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"159ea1d0-dacb-48e6-bbd4-801b0374c50b\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:44:08.928763418Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:44:38,928Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"63aaf767-62ea-4424-8b18-f0620a6a2abe\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:44:38.930205841Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:45:08,925Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"2d172263-35d4-4abb-a61f-eec39b5c536d\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:45:08.926820021Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:45:38,939Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"998560c9-0d99-42be-ab24-80e4b12b8778\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:45:38.940583201Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:46:08,933Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"909b214f-52ff-40d2-8a43-39b9c4ca10b7\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:46:08.934866998Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:46:38,933Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"b467e21d-6843-435f-b26b-3e6df1b1af71\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:46:38.934414916Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:47:08,927Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"5db92baf-c7bb-4fd0-af4a-2fc61e8b7287\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:47:08.92918319Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:47:38,177Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"b6475a7d-3449-4d18-b622-71cb1d794ddb\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:47:38.179883682Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:47:51,459Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"c16973c8-d413-4065-93d6-ed1f01f2c755\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:47:51.460779433Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:48:51,965Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"80c518a3-6c12-47f1-8269-28c291188720\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:48:51.966390317Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:52:19,918Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"b945e98e-cca3-4b31-86ce-b3691fb83543\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:52:19.91992172Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:54:57,019Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"2dacd284-0e77-4100-9f3a-72ce20f50226\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:54:57.020996352Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:55:08,356Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"8cdece07-3245-4db1-b210-335998c12d00\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:55:08.3579955Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:57:46,107Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"f14592b7-885f-4cb0-a704-e91130ebec10\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:57:46.107871092Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:58:04,285Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"fad3000d-f757-44b2-a7ca-1609f6608b77\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:58:04.286583518Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:58:13,310Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"04c20bf5-00f1-41cb-a8b7-af804f996a3a\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:58:13.311346354Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:58:14,445Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"0907be1e-fdc3-4a03-9643-b27b40ca555b\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:58:14.446608703Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:58:31,267Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"8d341914-34a7-4f6b-852e-e2f0a9a89f84\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:58:31.269055629Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T18:58:39,001Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"247b572b-7826-49fa-92fe-ff36e75ae670\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T18:58:39.003120227Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:01:19,466Z\", \"level\": \"WARN\", \"component\": \"o.e.d.r.RestController\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"Legacy index templates are deprecated in favor of composable templates.\", \"x-opaque-id\": \"1c97f1ea-9fe6-41a6-b301-04d3993fbd0f\", \"key\": \"deprecated_route_PUT_/_template/{name}\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:01:19.467681788Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:01:19,468Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestPutIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in put index template requests is deprecated. The parameter will be removed in the next major version.\", \"x-opaque-id\": \"1c97f1ea-9fe6-41a6-b301-04d3993fbd0f\", \"key\": \"put_index_template_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:01:19.468717641Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:01:19,715Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"b5cda49a-6025-4e7b-b7b8-2933d55f6885\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:01:19.716038825Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:01:19,949Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.r.a.a.i.RestGetIndexTemplateAction\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Specifying include_type_name in get index template requests is deprecated.\", \"x-opaque-id\": \"296641c3-6a64-4ecc-b3c0-f01c5eefcd99\", \"key\": \"get_index_template_include_type_name\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:01:19.95049504Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:01:31,159Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"afbf2dc7-0c35-46cd-9510-2c3c63886047\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:01:31.160832396Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:05:33,425Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"d1641205-d2db-48dd-869c-9121e54061aa\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:05:33.426561997Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:05:44,814Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"0c5bc85b-87ad-40d4-90b5-eca011f57163\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:05:44.825123523Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:06:04,176Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"a07c67d1-5157-46cb-a99c-f55523e90b2d\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:06:04.177644383Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:06:34,113Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"2601c886-96b2-486a-a937-939d82ea298e\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:06:34.114273441Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:11:53,325Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"5af1b87f-0b39-4986-b480-c6cb3147b1dc\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:11:53.327144627Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:11:58,611Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"dca24933-c2b2-449f-b5fc-ce7972b1493f\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:11:58.613262761Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:12:55,696Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"ef343b53-c953-40bd-9154-b04c4c0e37d1\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:12:55.69743981Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-15T19:14:24,295Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"cbf83e69-6359-497a-aee9-3b9b98bfc830\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-15T19:14:24.296174346Z"}
{"log":"{\"type\": \"server\", \"timestamp\": \"2022-04-16T03:36:03,016Z\", \"level\": \"WARN\", \"component\": \"o.e.t.ThreadPool\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"timer thread slept for [8.2h/29877098ms] on absolute clock which is above the warn threshold of [5000ms]\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:36:03.017352172Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:36:11,885Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:36:11.886477104Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:39:11,303Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"076425e5-0e98-49b4-9c05-27db4cffc1a4\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:39:11.304198826Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:39:14,729Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"fb2b64b4-d159-493f-a064-1f42ec9524a2\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:39:14.730256533Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:39:35,273Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"ac0fe13f-3679-4833-b90b-2a23ba237f9b\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:39:35.274473949Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:39:49,497Z\", \"level\": \"WARN\", \"component\": \"o.e.d.c.m.IndexNameExpressionResolver\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"this request accesses system indices: [.async-search, .tasks], but in a future major version, direct access to system indices will be prevented by default\", \"x-opaque-id\": \"67134f31-88e8-4c53-a564-100a462be0f3\", \"key\": \"open_system_index_access\", \"category\": \"api\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:39:49.498769236Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:51:12,601Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"a812f288-e36f-4462-b38a-578531845096\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:51:12.603650974Z"}
{"log":"{\"type\": \"deprecation.elasticsearch\", \"timestamp\": \"2022-04-16T03:51:28,108Z\", \"level\": \"CRITICAL\", \"component\": \"o.e.d.i.m.T.TypeFieldType\", \"cluster.name\": \"es-docker-cluster\", \"node.name\": \"es-warm\", \"message\": \"[types removal] Using the _type field in queries and aggregations is deprecated, prefer to use a field instead.\", \"x-opaque-id\": \"f443e092-c787-4374-9a9e-cfec47d679fe\", \"key\": \"query_with_types\", \"category\": \"types\", \"elasticsearch.elastic_product_origin\": \"kibana\", \"cluster.uuid\": \"V6JcOag3SEizV7t4nYi1Cg\", \"node.id\": \"uZM41So1RiSoKtHUGOwZUQ\"  }\n","stream":"stdout","time":"2022-04-16T03:51:28.109086603Z"}

```


* Файл `filebeat.yml`
```
filebeat.inputs:
  - type: container
    paths:
      - '/var/lib/docker/containers/*/*.log'

processors:
  - add_docker_metadata:
      host: "unix:///var/run/docker.sock"

  - decode_json_fields:
      fields: ["message"]
      target: "json"
      overwrite_keys: true

output.logstash:
  hosts: ["http://logstash:5046"]

#output.console:
#  enabled: true

logging.json: true
logging.metrics.enabled: false

```

6. Проверка того, что ФБ передает сообщения с логами.
[Set up and run Filebeatedit](https://www.elastic.co/guide/en/beats/filebeat/current/setting-up-and-running.html?baymax=rec&rogue=rec-1&elektra=guide)
[Filebeat Reference](https://www.elastic.co/guide/en/beats/filebeat/7.16/index.html)
[Configure the Logstash output](https://www.elastic.co/guide/en/beats/filebeat/current/logstash-output.html)

7. [Справочник по Logstash](https://www.elastic.co/guide/en/logstash/7.16/index.html)
```

```
