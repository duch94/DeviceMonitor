# DeviceMonitor

Задание:
Создать программу-демона (не http сервер) на Go, которая будет мониторить метрики устройств. В случае если значения метрик будут выходить за заданные пределы (указываются в конфиге), то требуется отправить уведомление по почте об этом. Также требуется сохранить в device_alerts и в Redis данное сообщение. В Redis должно хранится только последнее сообщение для устройства. Если в кеше уже есть сообщение для данного устройство, оно перезаписывается на более новое. Требуется обратить внимание на то, что возможный tps на device_metrics может достигать 2000.

Устройств около 10000. 
Метрики посылает устройство довольно часто, к примеру, каждые 5 секунд. Устройство за раз может прислать разное количество значений метрик, данные по отсутствующим метрикам, будут равны NULL.

В таблице users хранятся данные о пользователях
В таблице devices хранятся данные об устройствах,которые привязаны к пользователям.
В таблице device_metrics хранятся метрики устройств. 
В таблице device_alerts хранятся сообщения об ошибках

БД: PostgreSQL
Кеш: Redis

Схема таблиц:

```
CREATE TABLE users
(
id INT PRIMARY KEY,
name varchar(255),
email varchar(255) NOT NULL
)

CREATE TABLE devices 
(
id INT PRIMARY KEY,
name varchar(255) NOT NULL,
user_id INT NOT NULL,
CONSTRAINT devices_user_id_fk FOREIGN KEY(user_id) REFERENCES users (id) ON DELETE CASCADE
);

CREATE TABLE device_metrics
(
id INT PRIMARY KEY,
device_id INT NOT NULL,
metric_1 INT,
metric_2 INT,
metric_3 INT,
metric_4 INT,
metric_5 INT,
local_time TIMESTAMP, —Время метрик на устройстве
server_time TIMESTAMP DEFAULT NOW() — Серверноевремя сохранения метрик
CONSTRAINT device_metrics_device_id_fk FOREIGN KEY (device_id) REFERENCES devices (id) ON DELETE CASCADE
);

CREATE TABLE device_alerts
(
id INT PRIMARY KEY,
device_id INT,
message TEXT
)
```
 
