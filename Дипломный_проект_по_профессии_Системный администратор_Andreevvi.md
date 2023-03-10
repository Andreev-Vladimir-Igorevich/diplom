# Дипломный проект по профессии "Системный администратор" - Андреев Владимир Игоревич

Содержание

Задача

Инфраструктура

Сайт

Мониторинг

Логи

Сеть

Резервное копирование

Дополнительно

Выполнение работы

Критерии сдачи

Как правильно задавать вопросы дипломному руководителю?

# **Задача**

**Ключевая задача - разработать отказоустойчивую инфраструктуру для сайта, включающую мониторинг, сбор логов и резервное копирование основных данных. Инфраструктура должна размещаться в Yandex Cloud.**

# **Инфраструктура**

Для развертки инфраструктуры используйте **Terraform и Ansible.**

Параметры виртуальной машины (ВМ) подбирайте по потребностям сервисов, которые будут на ней работать.

Пожалуйста, ознакомьтесь со всеми пунктами из этой секции, не беритесь сразу выполнять задание, не дочитав до конца. Пункты тут взаимосвязаны и могут влиять друг на друга.

# **Сайт**

Создайте **две ВМ в разных зонах, установите на них сервер nginx**. 

ОС и содержимое ВМ должно быть идентичным, это будут наши web-сервера.

Используйте набор статичных файлов для сайта. Можно переиспользовать сайт из домашнего задания.

Создайте Target Group, включите в нее две созданных ВМ.

Создайте Backend Group, настройте backends на target group ранее созданную. Настройте healthcheck на корень (/) и порт 80, протокол HTTP

Создайте HTTP router. Путь укажите - /, backend group - созданную ранее.

Создайте Application load balancer для распределения трафика на web-сервера, созданные ранее. Укажите HTTP router созданный ранее, задайте listener тип auto, порт 80.

Протестируйте сайт curl -v <публичный IP балансера>:80

# **Мониторинг**

**Создайте ВМ, разверните на ней Prometheus**. 

На каждую ВМ из web серверов установите Node Exporter и Nginx Log Exporter. Настройте Prometheus на сбор метрик с этих exporter.

**Создайте ВМ, установите туда Grafana**.

 Настройте ее на взаимодейтсвие с ранее развернутым Prometheus. Настройте дешборды с отображением метрик, минимальный набор - Utilization, Saturation, Errors для CPU, RAM, диски, сеть, http_response_count_total, http_response_size_bytes. Добавьте необходимые tresholds на соответствующие графики.

# **Логи**

**Cоздайте ВМ, разверните на ней Elasticsearch.**

 Установите filebeat в ВМ к web-серверам, настройте на отправку access.log, error.log nginx в Elasticsearch.

**Создайте ВМ, разверните на ней Kibana**, сконфигурируйте соединение с Elasticsearch.

# **Сеть**

Разверните один VPC. Сервера web, Prometheus, Elasticsearch поместите в приватные подсети. Сервера Grafana, Kibana, application load balancer определите в публичную подсеть.

Настройте Security Groups соответствующих сервисов на входящий трафик только к нужным портам.

Настройте ВМ с публичным адресом, в которой будет открыт только один порт - **ssh**.

Настройте все security groups на разрешение входящего ssh из этой security group. Эта вм будет реализовывать концепцию bastion host. Потом можно будет подключаться по ssh ко всем хостам через этот хост.

# **Резервное копирование**

Создайте snapshot дисков всех ВМ. Ограничьте время жизни snaphot в неделю. Сами snaphot настройте на ежедневное копирование.


# Дополнительно

Не входит в минимальные требования.

Для Prometheus можно реализовать альтернативный способ хранения данных - в базе данных PpostgreSQL.

Используйте Yandex Managed Service for PostgreSQL. Разверните кластер из двух нод с автоматическим failover.

Воспользуйтесь адаптером с https://github.com/CrunchyData/postgresql-prometheus-adapter для настройки отправки данных из Prometheus в новую БД

Вместо конкретных ВМ, которые входят в target group можно создать Instance Group, для которой настройте следующие правила автоматического горизонтального масштабирования: минимальное количество ВМ на зону - 1, максимальный размер группы - 3.

Можно добавить в Grafana оповещения с помощью Grafana alerts. Как вариант, можно также установить Alertmanager в ВМ к Prometheus, настроить оповещения через него.

В Elasticsearch добавьте мониторинг логов самого себя, Kibana, Prometheus, Grafana через filebeat. 

Можно использовать logstash тоже.

Воспользуйтесь Yandex Certificate Manager, выпустите сертификат для сайта, если есть доменное имя. 

Перенастройте работу балансера на HTTPS, при этом нацелен он будет на HTTP web серверов.


# Выполнение работы

На этом этапе вы непосредственно выполняете работу. При этом вы можете консультироваться с руководителем по поводу вопросов, требующих уточнения.

warning В случае недоступности ресурсов Elastic для скачивания рекомендуется разворачивать сервисы с помощью docker контейнеров, основанных на официальных образах.

Важно: Еще можно задавать вопросы по поводу того, как реализовать ту или иную функциональность. И руководитель же определяет, правильно вы её реализовали или нет. Любые вопросы, которые не освещены в данном документе, стоит уточнять у руководителя. Если его требования/указания расходятся с указанными в данном документе, то приоритет имеют требования/указания руководителя.

# Критерии сдачи
Инфраструктура отвечает минимальным требованиям, описанным в Задаче.

Предоставлен доступ ко всем ресурсам, у которых предполагается веб-страница - **сайт, Kibana, Grafanа.**

Для ресурсов, к которым предоставить доступ проблематично, предоставлен скриншоты, команды, stdout, stderr, подтверждающий работу ресурса.

Работа выполнена в Google Docs, разрешен доступ по ссылке.

Работа оформлена так, чтобы были понятны ваши решения и компромиссы.

# **Если использованы дополнительные репозитории, то доступ к ним открыт (публичный репозиторий)**

Как правильно задавать вопросы дипломному руководителю?

Что поможет решить большинство частых проблем:

Попробовать найти ответ сначала самостоятельно в интернете или в материалах курса и только после этого спрашивать у дипломного руководителя. 

Скилл поиска ответов пригодится вам в профессиональной деятельности.

Если вопросов больше одного, то присылайте их в виде нумерованного списка.

Так дипломному руководителю будет проще отвечать на каждый из них.

При необходимости прикрепите к вопросу скриншоты и стрелочкой покажите, где не получается. 

Программу для этого можно скачать здесь https://app.prntscr.com/ru/

Что может стать источником проблем:

Вопросы вида «Ничего не работает. Не запускается. Всё сломалось». 

Дипломный руководитель не сможет ответить на такой вопрос без дополнительных уточнений. Цените своё время и время других.

Откладывание выполнения курсового проекта на последний момент.

Ожидание моментального ответа на свой вопрос. Дипломные руководители - работающие инженеры, которые занимаются, кроме преподавания, своими проектами. Их время ограничено, поэтому постарайтесь задавать правильные вопросы, чтобы получать быстрые ответы :)

____________
____________

# **Oтвет**

# **Задача**

**Ключевая задача - разработать отказоустойчивую инфраструктуру для сайта, включающую мониторинг, сбор логов и резервное копирование основных данных. Инфраструктура должна размещаться в Yandex Cloud.**

Доступ на яндекс облако по ссылке:

https://disk.yandex.ru/i/HG43YlH-DnzkJw

# **Инфраструктура**

Для развертки инфраструктуры используйте **Terraform и Ansible.**

Доступ на всю инфраструктуру :

https://disk.yandex.ru/d/Kv29_vdl0Iz9tw


# **Сайт**

Создайте **две ВМ в разных зонах, установите на них сервер nginx**  и содержимое ВМ должно быть идентичным, это будут наши web-сервера.

Устанавливаем на vm-web-1 - 10.0.1.7 и  vm-web-2 - 10.0.2.7 через ansible-playbook nginx.yaml 

Создаем Target Group, включите в нее две созданных ВМ.

Настраиваю healthcheck на корень (/) и порт 80, протокол HTTP . Создаем HTTP router. Путь указал - /.

Создаю Application load balancer для распределения трафика на web-сервера, созданные ранее.

Задаю listener тип auto, порт 80.

Тестирую сайт curl -v <публичный IP балансера>:80
___

Захожу на vm-web-1 через бастион host

![Снимок экрана от 2023-01-25 16-30-47](https://user-images.githubusercontent.com/94833070/214528195-f9dfec53-8191-4daa-9775-768e2b9f67d5.png)

таким же образом можно зайти на любую машину.

Проверяем работу сайта 

![Снимок экрана от 2023-01-25 16-34-30](https://user-images.githubusercontent.com/94833070/214528538-cf34c8e5-37c2-41f6-93ff-8ede16091487.png)


![Снимок экрана от 2023-01-24 23-42-43](https://user-images.githubusercontent.com/94833070/214354370-f23b2f94-b736-4341-8ca9-b64fc0f4db93.png)


Сайт работает)

# **Мониторинг**

**Создайте ВМ, разверните на ней Prometheus**. 

Устанавливаю на  ВМ: vm-web-1 - 10.0.1.7 и  vm-web-2 - 10.0.2.7 Node Exporter и Nginx Log Exporter через ansible-playbook nginxlog-exporter.yaml

и ansible-playbook node-exporter.yaml.

Устанавливаю Prometheus на vm-prometheus -10.0.5.9 через ansible-playbook prometheus.yaml

Метики доступны на данной машине 

![Снимок экрана от 2023-01-25 16-37-13](https://user-images.githubusercontent.com/94833070/214529116-f91c4329-35a1-4bda-8378-687b63618736.png)

Настраиваю Prometheus на сбор метрик с этих exporter.

**Создаю ВМ, и устанавливаю туда Grafana**. через ansible-playbook grafina.yaml

Запускаю grafana через публичный ip http://51.250.43.109:3000/

логин : admin
password : andreevvi

![Снимок экрана от 2023-01-25 16-43-28](https://user-images.githubusercontent.com/94833070/214530461-6245366d-c180-4624-ac26-7ca2ad991555.png)


 Проверяю и настраиваю взаимодейтсвие с ранее развернутым Prometheus.
 

![Снимок экрана от 2023-01-25 16-45-07](https://user-images.githubusercontent.com/94833070/214530859-63ff6635-99a6-474a-bf72-dc8e46a55754.png)


Добавляю дашборды Node Exporter и Nginx Log Exporter

Далее настраиваю дешборды с отображением метрик, минимальный набор - Utilization, Saturation, Errors для CPU, RAM, диски, сеть, http_response_count_total, http_response_size_bytes. 
 
Добавляю необходимые tresholds на соответствующие графики.
 
Вот так настраиваю nginx_http_response_count_total

![Снимок экрана от 2023-01-25 16-55-30](https://user-images.githubusercontent.com/94833070/214533086-e8b72aaf-b8c5-4213-a69a-bb15e6eaa070.png)


и добавляю необходимые tresholds на соответствующий график.

Остальные настраиваю по аналогии.

 Вот что получилось: как  видим есть приевышение
 
![Снимок экрана от 2023-01-25 23-51-26](https://user-images.githubusercontent.com/94833070/214825280-ab831c69-7a47-4b77-b2eb-bc8e59f84a93.png)

![Снимок экрана от 2023-01-25 23-51-37](https://user-images.githubusercontent.com/94833070/214825318-33ba73da-6d0e-49a3-a6d1-fc6eceffa3e8.png)

![Снимок экрана от 2023-01-26 00-02-32](https://user-images.githubusercontent.com/94833070/214825439-29ddaadf-796f-453b-81c5-c756031ab3fd.png)

![Снимок экрана от 2023-01-26 00-02-37](https://user-images.githubusercontent.com/94833070/214825479-fb62a8e6-b826-4515-a5cc-e9d95ac39639.png)


![Снимок экрана от 2023-01-26 18-24-22](https://user-images.githubusercontent.com/94833070/214825669-a3d67575-d2fe-481c-9899-d76fb69cf937.png)

![Снимок экрана от 2023-01-26 18-20-32](https://user-images.githubusercontent.com/94833070/214825703-19b571ab-197e-43e2-a402-1a4204472d8c.png)

![Снимок экрана от 2023-01-26 18-20-37](https://user-images.githubusercontent.com/94833070/214825748-e2b1a596-7e5b-4922-8757-5c5599e8edc7.png)

 Grafana все видит и все работает)

# **Логи**

**Cоздаю ВМ и разверачиваю на ней Elastic.**  vm-elastic : 10.0.5.11 и устанавливаю ansible-playbook elastic.yaml

 Установаю filebeat на машины vm-web-1 - 10.0.1.7 и  vm-web-2 - 10.0.2.7 через ansible-playbook filebeat.yaml

**Создаю ВМ и разверачиваю на ней Kibana** через ansible-playbook kibana.yaml 

Важно версия Elastic и Kibana должна быть  одной версии и савместима с OS.

Заходим через публичный ip Kibana http://51.250.38.24:5601

Доступ есть 

![Снимок экрана от 2023-01-25 16-58-59](https://user-images.githubusercontent.com/94833070/214534176-f83c24e8-7320-401f-9150-7b8b605483a4.png)


Добавляю Index patterns , как видим kibana видит filebeat


![Снимок экрана от 2023-01-25 01-21-41](https://user-images.githubusercontent.com/94833070/214534464-933efcb9-589d-42da-b29a-6c9ff8d70c9f.png)


Добавляем Discover для отображения логов nginx 

Видим логи nginx за час на серверах web-1  и  web-2 


![Снимок экрана от 2023-01-25 17-03-00](https://user-images.githubusercontent.com/94833070/214534745-a72d1102-3c01-4dce-98bf-9b05ff25333e.png)


# **Сеть**

Развернули  один VPC. 

![Снимок экрана от 2023-01-25 01-25-39](https://user-images.githubusercontent.com/94833070/214376924-97e0ce11-ee23-46aa-8ccd-7ffb5720100c.png)


Сервера web, Prometheus, Elasticsearch помещаю в приватные подсети.

Сервера Grafana, Kibana, application load balancer в публичной подсети  а так же bastion host 


![Снимок экрана от 2023-01-25 01-29-04](https://user-images.githubusercontent.com/94833070/214377671-18fdb9b9-f5c6-49ba-b0ad-2341293de403.png)

Группы безопасности: 

![Снимок экрана от 2023-01-25 01-31-43](https://user-images.githubusercontent.com/94833070/214378427-e3ddf3c8-2b72-4aa7-88e1-fc45bdfc52f8.png)


Настраиваю все security groups на разрешение входящего ssh через bastion host. Подключаться по ssh ко всем хостам только через этот хост по 22 порту.


![Снимок экрана от 2023-01-25 16-30-47](https://user-images.githubusercontent.com/94833070/214535327-5cb0f957-d51d-4c39-aa71-47c17f03d4d2.png)


Доступ через бастион работает.



# **Резервное копирование**

Создать snapshot дисков всех ВМ. Ограничить время жизни snaphot в неделю. Сами snaphot настроить на ежедневное копирование.

Создадим snapshot всех 7 дисков с ограничением времени жизни snaphot в неделю и сами snaphot настроем на ежедневное копирование

через terratorm манифест yandex_compute_snapshot_schedule.tf

![Снимок экрана от 2023-01-25 01-48-27](https://user-images.githubusercontent.com/94833070/214382459-00b252c1-104e-4b5f-b6c1-d9c79c430ee5.png)

Получилось расписание снимков example-schedule

![Снимок экрана от 2023-01-25 01-56-01](https://user-images.githubusercontent.com/94833070/214383477-34e5bb10-6dad-4103-8caa-8db76779bfa1.png)

Ровно в 19.00 по Лондону или 22.00 по Москве будут созданы snapshot всех дисков

Начались создаваться snapshot 

![Снимок экрана от 2023-01-25 02-09-30](https://user-images.githubusercontent.com/94833070/214386022-b0845166-2be5-4a84-8901-492a06dd0e6c.png)

и дальше создались все остальные 

![Снимок экрана от 2023-01-25 02-23-11](https://user-images.githubusercontent.com/94833070/214388670-d59cb9a9-5328-4d85-a320-aebd3b607bcf.png)


Как видим в назначенное время snapshot дисков всех ВМ создались!)) и будут создаваться 7 неделю.

спустя сутки 

![Снимок экрана от 2023-01-26 18-39-44](https://user-images.githubusercontent.com/94833070/214826677-07942911-559e-431b-8d0c-d4c147c95408.png)




# PS : Огромное спасибо за обучение нетологии.






