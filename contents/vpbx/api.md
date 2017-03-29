--- 
template: vpbx.jade
title: API & webhooks
order: 10
---

## API & webhooks

### API 

API позволяет взаимодействовать сторонним системам с сервисами Виртуальной АТС. 

Для совершения действий вам необходимо узнать соответствующий ключ пользователя портала Виртуальной АТС. Пользователь портала Виртуальной АТС должен быть связан с соответствующим абонентом или оператором Виртуальной АТС. 

#### Инициирование вызова

Для совершения вызова с абонента Виртуальной АТС на определенный номер можно сделать http запрос. После запроса придет вызов на абонента, а после ответа абонента вызов пойдет на вызываемый номер.


`````
https://connect.mobilon.ru/api/call/CallToSubscriber?key={userkey}&outboundNumber={number}

`````
где {userkey} - это ключ пользователя, {number} - вызываемый номер.

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
  "description": "Operator is not registered",
  "code": "1"
}

`````

Коды ошибок:

- 1 => 'Operator is not registered'

- 2 => 'Invalid API key'

- 3 => 'The number can only consist of numbers and Latin characters'

- 4 => 'Internal error'

- 5 => 'User doesn\'t have subscriber or operator'



#### curl

`````
curl -X GET "https://connect.mobilon.ru/api/call/CallToSubscriber?key={userkey}&outboundNumber={number}"

`````

#### Полигон

[попробовать онлайн-запрос](https://antirek.github.io/mobilon-demo)

#### Пример для 1С 8.х

Повешайте функцию на кнопку

`````
// key - ключ сотрудника, полученный в ВАТС
// number - номер, на который нужно позвонить

&НаСервере
Процедура СовершитьЗвонокВеб(number,key) Экспорт       
    Если не ЗначениеЗаполнено(number)ИЛИ не ЗначениеЗаполнено(key) Тогда
        Возврат;                            
    КонецЕсли;
   
    ssl = Новый ЗащищенноеСоединениеOpenSSL( неопределено, неопределено );
    НТТР = Новый HTTPСоединение("connect.mobilon.ru",,,,,,ssl);
    Запрос = Новый HTTPЗапрос("/api/call/CallToSubscriber/?key="+СокрЛП(key)+"&outboundNumber="+СокрЛП(number));
    Ответ = НТТР.Получить(Запрос);
   
    Если Ответ.КодСостояния<>200 Тогда
        Сообщить("Не удалось совершить исходящий вызов");
    КонецЕсли;
КонецПроцедуры



`````


#### получение информации о звонке по callid

В результате выполнения инициации звонка возвращается callid, по которому позже можно получить информацию о звонке

`````
https://connect.mobilon.ru/api/call/info?token={token}&callid={callid}&format={json|xml}

`````

- {token} - токен аккаунта, 
- {callid} - id звонка, 
- {json|xml} - формат возвращаемых данных json или xml

Возвращает результат

`````
<?xml version="1.0" encoding="UTF-8"?>
<call>
  <callid>1324324.8997834</callid>
  <status>ANSWERED</status>
  <record_url>/api/call/record?token={token}&callid={callid}</record_url>
  <duration>203</duration>
  <from>81234567890</from>
  <to>123123</to>
  <time>2015-04-28 12:00:00</time>
</call>

`````

xml содержит ссылку на запись звонка



#### получение информации о звонках за период

Есть возможность получить информацию о звонках за определенный день

`````
https://connect.mobilon.ru/api/call/journal?token={token}&date={date}&format={json|xml}

`````

- {token} - токен аккаунта, 
- {date} - дата (в формате ГГГГ-ММ-ДД),
- {json|xml} - формат возвращаемых данных json или xml

Возвращает результат

`````
<?xml version="1.0" encoding="UTF-8"?>
<calls>
  <call>
    <callid>32423423.324324</callid>
    <status>ANSWERED</status>
    <duration>113</duration>
    <from>81234567890</from>
    <to>987315</to>
    <time>2015-04-28 13:12:00</time>
  </call>
  <call>
    <callid>32423423.323242</callid>
    <status>ANSWERED</status>
    <record_url>/api/call/record?token={token}&callid={callid}</record_url>
    <duration>203</duration>
    <from>81234567890</from>
    <to>123123</to>
    <time>2015-04-28 12:00:00</time>
  </call>
</calls>

`````



### Webhooks 

Веб-хуки - адреса в сети интернет, которые могут быть запрошены во время обработки звонков Виртуальной АТС.


#### Получение звонков абонентами

В настройках Виртуальной АТС укажите веб-адрес системы, куда необходимо передавать информацию о звонке.

Также можно указать {NUMBER] - номер звонящего абонента, и {KEY} - ключ абонента, получившего вызов.

![](images/webhooks_1.png)

Результат запроса

![](images/webhooks_2.png)



#### JS обертка для Mobilon VPBX API (API Виртуальной АТС Мобилон)

[Github](https://github.com/antirek/mobilon-vpbx-api.js)
