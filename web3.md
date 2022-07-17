# BlockPI_monitoring ![Знімок екрану з 2022-07-17 08-56-01](https://user-images.githubusercontent.com/102728347/179386027-b97e9095-fa40-4540-8c22-c32c41f165e8.png) 

Послідовнічть дій:
* [Встановлюємо grafana і prometheus на сервер де не працює BlockPI](https://github.com/MaxMavaIll/BlockPI_monitoring/blob/main/web3.md#%D0%B2%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BB%D1%8E%D1%94%D0%BC%D0%BE-grafana-%D1%96-prometheus-%D0%BD%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80-%D0%B4%D0%B5-%D0%BD%D0%B5-%D0%BF%D1%80%D0%B0%D1%86%D1%8E%D1%94-blockpi)
* Встановлюємоно node-exporter насервер де знаходться BlockPI
* Налаштування конфігурації prometheus
* Налаштування Grafana
* Настройка панелі HyperNode



# Встановлюємо grafana і prometheus на сервер де не працює BlockPI.

### Заходимо в root користувача
```
sudo su

```

### Спочатку оновлюємо пакети
```
sudo apt update && sudo apt upgrade -y

```
![Image text](https://github.com/cybernekit/RouterSetupGuide/blob/main/img/Screenshot%20from%202022-05-17%2016-49-11.png)
### Установлюємо docker
(не обов'язково якщо встановлений docker)
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce

```
### Установлюємо git
(не обов'язково якщо встановлений git)
```
apt install git

```

### Завантажуємо docker-compose.yml
```
cd ~
git clone https://github.com/cybernekit/Grafana-Prometheus.git
cd ~/Grafana-Prometheus

```
![Screenshot from 2022-05-28 13-24-48](https://user-images.githubusercontent.com/59205554/170821373-17d41ca2-0a57-4721-a64d-1dce8ee9f8a3.png)

### Створюємо контейнер
```
sudo docker swarm init
sudo docker stack deploy -c ~/Grafana-Prometheus/docker-compose.yml monitoring

```

![Screenshot from 2022-07-16 16-00-16](https://user-images.githubusercontent.com/102728347/179355943-5608a86a-2b56-4a56-b2f7-b31afaabc575.png)


### Провіряємо чи запустилися контейнери
Не спіши запускати зачикай деякий час щоб воно все запустило.

```
sudo docker ps

```

![Screenshot from 2022-05-28 13-35-21](https://user-images.githubusercontent.com/59205554/170821748-022e38d8-d824-465a-8979-334cff2ca31f.png)

 
# Встановлюємо node-exporter на сервер де знаходться BlockPI
Встановлюємо node-exporter через таку команду
```
wget https://raw.githubusercontent.com/MaxMavaIll/BlockPI_monitoring/main/node-exporter.sh  && chmod +x node-exporter.sh && bash node-exporter.sh

```


# Налаштування конфігурації prometheus
Переходимо до конфігурацюї promtheus. Файл називадться `promtheus.yml` і запінюємо його на файл з правильною конфігурацюєю.
```
cd /var/lib/docker/volumes/monitoring_prom-configs/_data
rm prometheus.yml
wget https://raw.githubusercontent.com/MaxMavaIll/BlockPI_monitoring/main/prometheus.yml

```

Тепер замінюємо в цьому файлі `your_address` на ваш адрес
```
sed -i 's/your_address/<Your_address>/' /var/lib/docker/volumes/monitoring_prom-configs/_data/prometheus.yml
```
### Перезапускаємо prometheus
Для того щоб перезапустити прометеус нам потрібно дізнатися id контейнера
```
sudo docker ps

```
![Screenshot from 2022-07-16 15-17-24](https://user-images.githubusercontent.com/102728347/179354582-efc6efda-bd83-4a37-93c6-0f3a2b43e6e8.png)

```
sudo docker restart <CONTAINER ID>
```
Все ви підняли prometheus. 
Тепер щоб провірити чи він в справному стані, ми водимо в браузері.
```
http://<your_address_grafana>:9090
```
(Error -> Якщо виводить на екран що сторінку не знайдено зачекайте трішки і спробуйте знову увести свій адрес з портом)

Натискамо Status -> Targets

![tearTmY](https://user-images.githubusercontent.com/102728347/179355096-409b3161-6675-43d9-b543-80b9ecafb370.jpeg)

Якщо у вас такий вигляд то все підключилося і правильно працює 

![Screenshot from 2022-07-16 15-36-24](https://user-images.githubusercontent.com/102728347/179355199-eed91018-6d6c-49bc-a3e3-463b04f64932.png)

# Налаштування Grafana

Відкриваємо google на своєму компютері. 

Водимо адрес через браузер з портом `3000`:
```
http://<IP_address>:3000
```
Ми побачимо наступне(Звичайно якщо все правильно встановилося)

![ngazd4U](https://user-images.githubusercontent.com/102728347/179351515-3004bcf9-edff-4445-8658-416eadf7e41d.jpeg)

Позамовчуванню user -> `admin`; password -> `admin`.
Далі ви вам запропонує встановити свій пароль, grafana запише його в базу даних і з наступного разу, щоб зайти на свою grafana потрібно буде вести ці дані.

Коли зайшли в графану натискаємо шистиграник -> ![Screenshot from 2022-07-16 16-27-06](https://user-images.githubusercontent.com/102728347/179356902-73f0009d-36bd-49f7-b012-3516869bebdd.png) 

add data source -> ![Screenshot from 2022-07-16 16-28-33](https://user-images.githubusercontent.com/102728347/179356942-de8fa026-0365-43a6-9a3f-6d52c37d9450.png)

prometheus
![Screenshot from 2022-07-16 16-44-54](https://user-images.githubusercontent.com/102728347/179357543-57fea3cc-e144-47c3-878a-d5f11790accf.png)

У строку url вставляємо такий адрес http://prometheus:9090
Далі зберізаємо натиснувши знизу `save & test`

Якщо ти отримав зелуну галочку із назвою
`Data source is working`

# Настройка панелі HyperNode

Натискаємо плюс -> import

Ось що ми побачимо  
![Screenshot from 2022-07-16 17-04-14](https://user-images.githubusercontent.com/102728347/179358209-ecd023cf-1ca3-47f9-82b5-5c20b5ceb039.png)

Вибираємо яку хочемо загрузити таблицю від розробників BlockPI
і вставляємо її в поле `Import via panel json`

* [HyperNode](https://github.com/MaxMavaIll/BlockPI_monitoring/blob/main/HyperNode.json)

Самого початку вам викине помилку, але для того щоб все запрацювало потрібно зайти в `dashboard settings` -> `Variables`

![AOfRysh](https://user-images.githubusercontent.com/102728347/179372068-436382f2-18b6-457d-be8a-7d39afc12751.jpeg)

![Screenshot from 2022-07-17 00-18-46](https://user-images.githubusercontent.com/102728347/179372230-96412459-f16d-4c85-bd81-8710e044e1c5.png)

Тепер нам потрібно зайти в вкожний із цих варіантів, виправити `Data source` на prometheus і зберегти `Update`

![Screenshot from 2022-07-17 00-23-24](https://user-images.githubusercontent.com/102728347/179372346-c3cde8c1-49b6-45d0-ac90-1a5e42a37f67.png)

Виходимо на основний екран і натискаємо `Klaynt node status` -> `edit`, а потім также `Klaynt node hight` -> `edit` і `klaytn use memory` -> `edit`
![Screenshot from 2022-07-17 00-27-14](https://user-images.githubusercontent.com/102728347/179372502-fec90a30-9cb4-4242-9016-0ff83a739dba.png)

Також вибираєте prometheus і натискаєте `apply`
![Screenshot from 2022-07-17 00-32-41](https://user-images.githubusercontent.com/102728347/179372586-52b32e64-d397-4d31-8328-9d34fa1d3960.png)


# Вітаю тепер ти можеш відсліковувати свої характеристики

