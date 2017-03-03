---
template: autocall.jade
title: Автообзвон
order: 10
---

## Автообзвон API (Гарант)

Используется basic http аутентификация.

Логин, пароль, исходящий номер предоставляются Мобилон.


### Создание заданий

POST-запрос с массивом объектов в JSON-формате. Количество объектов не более 100.

Параметры объекта:

- guid - произвольный id
- file - имя файла для проигрывания
- date - дата проигрывания, необязательное, формат ГГГГ-ММ-ДД
- time - время проигрывания, необязательное, формат ЧЧ:ММ
- number - вызываемый номер, формат 8NNNNNNNNNN
- source - исходящий номер, формат 8NNNNNNNNNN

Запрос

`````sh
curl -X POST -H "Content-Type: application/json" \
    -H "Authorization: Basic dGVzdFVzZXI6dGVzdFBhc3N3b3Jk" \
    -H "Cache-Control: no-cache" \
    -d '[{
            "guid": "7802d99b-3917-81ac-e0b7-52d5d44e6cd3",
            "file": "hello",
            "date": "2017-03-01",
            "time": "12:00",
            "number": "8913529XXXX",
            "source": "8923456XXXX"
        },{
            "guid": "70be9f43-1cbd-42fc-977f-416b1561e07e",
            "file": "hello",
            "date": "2017-03-01",
            "time": "12:00",
            "number": "8902980XXXX",
            "source": "8923456XXXX"
        }]' \
    "https://autocall2g.services.mobilon.ru/call/"

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


### Статус заданий

POST-запрос с массивом объектов в JSON-формате. Количество объектов не более 100.

Параметры объекта:

- id - id задания

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
    "https://autocall2g.services.mobilon.ru/status/"

`````

Ответ

`````
[
  {
    "status": 1,
    "guid": "7802d99b-3917-81ac-e0b7-52d5d44e6cd3",
    "dateExec": "2017-02-22T16:57:24.705Z",
    "id": "58a532240312d84a8100de5c"
  },{
    "status": 1,
    "guid": "70be9f43-1cbd-42fc-977f-416b1561e07e",
    "dateExec": "2017-02-22T17:03:04.129Z",
    "id": "58a532240312d84a810324sd"
  }
]

`````