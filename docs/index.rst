Введение
========
В этом руководстве описывается использование API для работы с файлами Depositphotos. Это руководство предназначено для разработчиков, знакомых с технологиями передачи данных HTTP, JSON, и имеющих базовые знания программирования на любом языке программирования.

API - это интерфейс доступа к приложению, который позволяет зарегистрированным клиентам работать с контентом и лицензиями Depositphotos. 

Для доступа к API endpoint требуется соответствующий ключ API. Ключ API обеспечивает его доступ к функциональности API, вместе с логином и паролем. Детальнее будет описано ниже.

Enterprise API включает функции для поиска файлов в базе данных Depositphotos, лицензирования контента, формирования счетов и лицензий, передачи прав (лицензий), и управление рабочими группами в рамках одной компании.

Главная информация
------------------
Регистрация
Чтобы стать партнером Depositphotos, вам необходима учетная запись Enterprise на Depositphotos и ключ доступа к Enterprise API (его можно получить у менеджера). 

После получения API-ключа можно приступать к работе с Enterprise API.

Общие принципы
--------------
Все вызовы API выполняются через HTTP-протокол с набором параметров GET или POST. Ответ API возвращает в формат JSON.

**Для всех методов API есть два обязательных параметра**

"dp_apikey" - ключ API, выдаётся пользователю менеджером, после регистрации в Depositphotos
"dp_command" - идентификатор метода (команды) API

Для методов, требующих управления сеансом пользователя, необходим следующий обязательный параметр:
"dp_sessionid" - уникальный идентификатор сеанса (сессии), возвращаемый методом входа

Все методы API могут возвращать два разных результата: успех («success») и ошибка («failure»). 
Результат «success» содержит параметры, специфичные для конкретного метода API, но приведенные ниже параметры являются общими для всех методов API:

"timestamp" - текущая дата и время в формате «ГГГГ-ММ-ДД ЧЧ:ММ:СС»
"version" - версия API, на данный момент актуальная версия 1.3
"type" - «успех» для успешных запросов, «ошибка», если возникают некоторые ошибки
