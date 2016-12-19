--- 
template: vpbx.jade
title: API & webhooks
order: 10
---

## API & webhooks

### API 

API позволяет взаимодействовать сторонним системам с сервисами Виртуальной АТС. 

Для совершения действий вам необходимо узнать соответствующий ключ абонента (пользователя портала Виртуальной АТС). 

#### Инициирование вызова

Для совершения вызова с абонента Виртуальной АТС на определенный номер можно сделать http запрос. После запроса придет вызов на абонента, а после ответа абонента вызов пойдет на вызываемый номер.


`````
http://connect.mobilon.ru/api/call/CallToSubscriber?key={userkey}&outboundNumber={number}

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
curl -X GET "http://connect.mobilon.ru/api/call/CallToSubscriber?key={userkey}&outboundNumber={number}"

`````

#### Полигон

[попробовать онлайн-запрос](https://antirek.github.io/mobilon-demo)

#### Пример для 1С 8.х

Повешайте функцию на кнопку

`````
// key - ключ сотрудника, полученный в ВАТС
// number - номер, на который нужно позвонить
&НаСервере
Процедура Позвонить(key, number)
    НТТР = Новый HTTPСоединение("connect.mobilon.ru");
    Запрос = Новый HTTPЗапрос("/api/call/CallToSubscriber/?key="+СокрЛП(key)+"&outboundNumber="+СокрЛП(number));
    Ответ = НТТР.Получить(Запрос);

    Если Ответ.КодСостояния <> 200 Тогда
        Сообщить("Не удалось совершить исходящий вызов");
    КонецЕсли;
КонецПроцедуры

`````


#### получение информации о звонке по callid

В результате выполнения инициации звонка возвращается callid, по которому позже можно получить информацию о звонке

`````
http://connect.mobilon.ru/api/call/info?token={token}&callid={callid}

`````
где {token} - это токен аккаунта, {callid} - id звонка.

Возвращает результат

`````
<?xml version="1.0" encoding="UTF-8"?>
<call>
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
http://connect.mobilon.ru/api/call/info?token={token}&date={date}

`````
где {token} - это токен аккаунта, {date} - дата (в формате ГГГГ-ММ-ДД).

Возвращает результат

`````
<?xml version="1.0" encoding="UTF-8"?>
<calls>
  <call>
    <status>ANSWERED</status>
    <duration>113</duration>
    <from>81234567890</from>
    <to>987315</to>
    <time>2015-04-28 13:12:00</time>
  </call>
  <call>
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



<script type="text/javascript" src="https://antirek.github.io/pokemon/pokemon.js"></script>
<script type="text/javascript">
  document.addEventListener('DOMContentLoaded', function() {
    pokemon('aerodactyl', '/developer/', {
      top: '100px',
      right: '120px',
    });
  });
</script>





# Интеграция с 1С

1С может быть интегрирована с Виртуальной АТС Мобилон посредством веб-сервисов 1С. [Веб-сервисы 1С](http://v8.1c.ru/overview/Term_000000273.htm) реализуются на основе WSDL/SOAP интерфейса. 1С публикует список доступных методов и параметров, а внешняя система может их использовать. 

Для интеграции необходимо реализовать два метода: один для получения информации о звонящем (по номеру звонящего) и второй для отображения карточки клиента (по номеру звонящего).


## Получение информации о звонящем 

Происходит получение имени абонента из 1С БД по номеру звонящего.

Необходимо реализовать интерфейс веб-сервиса, который будет возвращать имя.

Пример запроса от Виртуальной АТС Мобилон (в секции number передается номер звонящего):

`````
curl -X POST -H "Content-Type: text/xml" \
     -H "Authorization: Basic U09BUDoxMjM=" \
     -H "Cache-Control: no-cache" \
     -d '<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <SayName xmlns="http://test">
            <number>89235292926</number>
        </SayName>
    </soap:Body>
</soap:Envelope>
' "http://<ваш ip>:8080/telephony/ws/ImportTasks" 

`````

Пример ответа от веб-сервиса 1С:

`````
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <m:SayNameResponse xmlns:m="http://test">
            <m:return xmlns:xs="http://www.w3.org/2001/XMLSchema" 
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">VIP:Ренессанс Капитал</m:return>
        </m:SayNameResponse>
    </soap:Body>
</soap:Envelope>

`````

## Отображение карточки о звонящем

В интерфейсе 1С пользователя 1С появляется карточка абонента во время звонка.

Необходимо реализовать интерфейс веб-сервиса, который будет принимать данные во время звонка.

Пример запроса от Виртуальной АТС Мобилон (в секции number передается номер звонящего, в секции key - ключ абонента):

`````
curl -X POST -H "Authorization: Basic U09BUDoxMjM=" -H "Content-Type: text/xml" -H "Cache-Control: no-cache" -d '<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <IncomingCall xmlns="http://VATS">
            <NumFrom>89235292926</NumFrom>
            <Key>gW3cyUJAtl50PhF</Key>
        </IncomingCall>
    </soap:Body>
</soap:Envelope>
' "http://<ваш ip>/telephony/ws/VATS" 

`````

Пример ответа от веб-сервиса 1С:

`````
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <soap:Body>
        <m:IncomingCallResponse xmlns:m="http://VATS">
            <m:return xmlns:xs="http://www.w3.org/2001/XMLSchema" 
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">1</m:return>
        </m:IncomingCallResponse>
    </soap:Body>
</soap:Envelope>

`````

[Инструкция](attachments/MobilonVATS-1C-integration.pdf) - здесь рассказано как реализовать появление карточки со стороны 1С.


*** Внимание. Указанные  выше примеры могут отличаться от используемых в реальных системах в зависимости от версий ПО и приведены с целью ознакомления. Пожалуйста, в случае вопросов свяжитесь с технической поддержкой Виртуальной АТС Мобилон.