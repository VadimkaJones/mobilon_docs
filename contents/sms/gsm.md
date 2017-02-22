--- 
template: sms.jade
title: GSM
order: 70
---

## GSM API

Используется basic http аутентификация.

Логин, пароль, номер отправления предоставляются Мобилон.


### Отправка сообщений

POST-запрос с массивом объектов в JSON-формате. Количество объектов не более 100.

Параметры объекта:

- guid - произвольный id
- text - текст сообщения
- number - номер назначения
- source - номер отправления

Запрос

`````sh

curl -X POST -H "Content-Type: application/json" \
    -H "Authorization: Basic dGVzdFVzZXI6dGVzdFBhc3N3b3Jk" \
    -H "Cache-Control: no-cache" \
    -d '[{
            "guid": "7802d99b-3917-81ac-e0b7-52d5d44e6cd3",
            "text": "привет, мир!",
            "number": "8913529XXXX",
            "source": "8923456XXXX"
        },{
            "guid": "70be9f43-1cbd-42fc-977f-416b1561e07e",
            "text": "привет, мир 2!",
            "number": "8902980XXXX",
            "source": "8923456XXXX"
        }]' \
    "https://sms2g.services.mobilon.ru/send/"

`````

Ответ

`````
[
  {
    "status": 0,
    "guid": "7802d99b-3917-81ac-e0b7-52d5d44e6cd3",
    "id": "58a532240312d84a8100de5c"
  },{
    "status": 0,
    "guid": "70be9f43-1cbd-42fc-977f-416b1561e07e",
    "id": "58a532240312d84a810324sd"
  }
]

`````


### Статус сообщений

POST-запрос с массивом объектов в JSON-формате. Количество объектов не более 100.

Параметры объекта:

- id - id сообщения, возвращенный в запросе на отправку

Запрос

`````sh

curl -X POST -H "Content-Type: application/json" \
    -H "Authorization: Basic dGVzdFVzZXI6dGVzdFBhc3N3b3Jk" \
    -H "Cache-Control: no-cache" \
    -d '[{
            "id": "58a532240312d84a8100de5c"
        },{
            "id": "58a532240312d84a810324sd"
        }]' \
    "https://sms2g.services.mobilon.ru/status/"

`````

Ответ

`````
[
  {
    "status": 1,
    "guid": "7802d99b-3917-81ac-e0b7-52d5d44e6cd3",
    "dateTimeSent": "2017-02-22T16:57:24.705Z",
    "id": "58a532240312d84a8100de5c"
  },{
    "status": 1,
    "guid": "70be9f43-1cbd-42fc-977f-416b1561e07e",
    "dateTimeSent": "2017-02-22T17:03:04.129Z",
    "id": "58a532240312d84a810324sd"
  }
]

`````