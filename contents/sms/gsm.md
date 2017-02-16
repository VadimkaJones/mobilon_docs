--- 
template: sms.jade
title: GSM
order: 70
---

## GSM API

Используется basic http аутентификация.

Логин, пароль, номер отправления предоставляются Мобилон.

### Отправка СМС

POST-запрос с массивом объектов в JSON-формате.

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
    "https://sms2g.services.mobilon.ru/messages/"

`````

Ответ

`````

`````


### Получение статуса

POST-запрос с массивом объектов в JSON-формате.

