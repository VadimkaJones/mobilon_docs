--- 
template: vpbx2.jade
title: API
order: 10
---

## API

#### Инициирование вызова

Для совершения вызова с абонента Виртуальной АТС на определенный номер можно сделать http запрос. После запроса придет вызов на абонента, а после ответа абонента вызов пойдет на вызываемый номер.


`````
https://webapi2.services.mobilon.ru/call/{userkey}/{number}

`````
где {userkey} - это ключ абонента, {number} - вызываемый номер.

Результат выполнения запроса приходит в формате [JSON](http://ru.wikipedia.org/wiki/JSON). 

Успешное выполнение
`````
{
  "result":"SUCCESS",
  "description":"Call had been initialised",
  "code":0,
  "callid":"1476087194.216329"
}
`````

Неуспешное выполнение
`````
{
  "result": "FAIL", 
  "message": "Неверный ключ"
}

`````

#### curl

`````
curl -X GET "https://webapi2.services.mobilon.ru/call/{userkey}/{number}"

`````