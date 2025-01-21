# Установить Linux Oracle на VirtualBox:


Установить образ Linux

![image](https://github.com/user-attachments/assets/f2ea11bb-e666-4017-af76-871a19326a3f)


Выделить 4 ядра

Выделить 4096 МБ оперативной памяти

![image](https://github.com/user-attachments/assets/a0e8593d-abba-4b9c-8689-bf6afedecbc3)



# Установка docker

Устанавливает утилиту wget на систему

     sudo yum install wget

![image](https://github.com/user-attachments/assets/0cf7422c-2469-4090-9fc0-dc1f8ae59023)


Скачиваем файл репозитория

     sudo wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

![image](https://github.com/user-attachments/assets/ac0994f7-96f4-4022-a00e-4c40ce8ede7c)


Устанавливаем docker

     sudo yum install docker-ce docker-ce-cli containerd.io

![image](https://github.com/user-attachments/assets/ba739125-d94c-4afa-9cb2-ad2e731eed27)


Запускаем его и разрешаем автозапуск

     sudo systemctl enable docker --now

![image](https://github.com/user-attachments/assets/c4ec165b-003c-4f84-bad5-5750f0f168d5)



# Установка compose

Для начала нужно убедиться в наличии пакета curl

     sudo yum install curl

![image](https://github.com/user-attachments/assets/4b75781a-5059-40fb-b2b5-276cd67f2691)



Объявление переменной COMVER, полученной в результате curl запроса, хранящей в себе номер последней
версии Docker Compose

     COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)

Теперь скачиваем скрипт docker-compose последней версии, используя объявленную ранее переменную и помещаем его в каталог /usr/bin

     sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose

![image](https://github.com/user-attachments/assets/58659771-f5b1-4906-bb1e-bb33b060e79d)



Предоставление прав на выполнение файла docker-compose

     sudo chmod +x /usr/bin/docker-compose

Проверка установленной версии Docker Compose

     sudo docker-compose --version

![image](https://github.com/user-attachments/assets/fe4715ed-e75e-443d-8481-1d1f05346164)


# Делаем grafana

Установка git

     sudo yum install git

![image](https://github.com/user-attachments/assets/9f1f99ff-fe63-4337-9164-fa25cdb0eecb)



Этот код скачивает содержимое репозитория skl256/grafana_stack_for_docker

     sudo git clone https://github.com/skl256/grafana_stack_for_docker.git




Заходит в папку - cd

     cd grafana_stack_for_docker

cd .. - возвращает в папку выше

![image](https://github.com/user-attachments/assets/69755d2e-ccab-47e1-bdb6-5c3d59ae6a4e)



(После этого можно вставлять готовый docker-compose)

Cоздаем папки двумя разными способами

     sudo mkdir -p /mnt/common_volume/swarm/grafana/config

     sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}

Выдаем права

     sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}

Создаем файл

     sudo touch /mnt/common_volume/grafana/grafana-config/grafana.ini

Копирование файлов

     sudo cp config/* /mnt/common_volume/swarm/grafana/config/

Переименовывание файла

     sudo mv grafana.yaml docker-compose.yaml

![image](https://github.com/user-attachments/assets/6bad076e-bb16-4a52-83ab-2cd75acbdf7f)



Собрать докер (нужно запускать из папки где docker-compose.yaml)

     sudo docker compose up -d

Опустить докер - sudo docker compose stop


![image](https://github.com/user-attachments/assets/606226d2-0dca-4514-b8fa-f34cb458cc03)



# Начинаем чистку файлов

Команда открывает файл docker-compose.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi docker-compose.yaml

Что-бы что-то изменить в тесковом редакторе нужно нажать insert на клавиатуре

Затем в docker-compose нужно вставить node-exporter и удалить ненужные файлы (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/74219a47-f478-4eb9-bd32-692b7c841022)


выйти не сохраняясь из vim - esc -> :q!

выйти сохраняясь из vim - esc -> :wq!

Заходим в другую папку 

     sudo cd /mnt/common_volume/swarm/grafana/config

Открываем файл prometheus.yaml в текстовом редакторе vi с правами суперпользователя

     sudo vi prometheus.yaml

![image](https://github.com/user-attachments/assets/26938f61-e6d8-4cb6-9c5f-8eda3642a1c6)



Далее нужно исправить targets: на exporter:9100

![image](https://github.com/user-attachments/assets/7ab40cdc-396e-4cda-914d-11358d80f1a6)



# Делаем grafana на сайте

   •	переходим на сайт localhost:3000

                User & Password GRAFANA: admin

                Код графаны: 3000

                Код прометеуса: http://prometheus:9090

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку +Add visualization, а после "Configure a new data source"

                выбираем Prometheus

                Connection

                http://prometheus:9090

   •	Authentication

                Basic authentication

                User: admin

                Password: admin

                Нажимаем на Save & test и должно показывать зелёную галочку

   •	в меню выбираем вкладку Dashboards и создаем Dashboard

                ждем кнопку "Import dashboard"

                Find and import dashboards for common applications at grafana.com/dashboards: 1860 //ждем кнопку Load

                Select Prometheus ждем кнопку "Import"


![image](https://github.com/user-attachments/assets/a0b42721-4d4f-487d-a6fb-27400027959e)











# Делаем VictoriaMetrics

Для начала зайдем в нужную папку

     cd grafana_stack_for_docker

Открываем docker-compose

     sudo vi docker-compose.yaml

После prometheus вставляем vmagent (но у нас уже вставлен готовый докер)

![image](https://github.com/user-attachments/assets/9ec8aabf-537e-4ff1-908e-026cfd3061b3)



Захом в connection там где мы писали http://prometheus:9090 пишем http://victoriametrics:8428 И заменяем имя из "Prometheus-2" в "Vika" нажимаем на dashboards add visualition выбираем "Vika" снизу меняем на "code" Переходим в терминал и пишем

     echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus

• команда отправляет бинарные данные (например, метрики в формате Prometheus) на локальный сервер, который слушает на порту 8428.

     curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'

• команда делает запрос к API для получения данных по метрике OILCOINT_metric1

• команда выводит текст, который может быть использован для определения метрики в формате, совместимом с Prometheus

• команда выводит информацию о типе и значении этой метрики в формате, который может быть использован системой мониторинга Prometheus.

![image](https://github.com/user-attachments/assets/6682198c-a0c6-43b4-86d1-b4bd64600384)


Значение 0 меняем на любое другое

Копируем переменную OILCOINT_metric1 и вставляем в query
![image](https://github.com/user-attachments/assets/7d421e97-0ee8-432d-980b-6a2d6e8086fe)

Нажимаем run



Копируем переменную OILCOINT_metric1 и вставляем в code

![image](https://github.com/user-attachments/assets/44d1f7b3-25b9-4999-95c7-e34e4ed70ad8)


