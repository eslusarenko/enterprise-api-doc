.. figure:: _static/depositphotos_logo.png

*************************************
Depositphotos Enterprise Solution API
*************************************

`Enterprise Solution <https://depositphotos.com/enterprise.html>`_

Введение
========
В этом руководстве описывается использование API для работы с файлами Depositphotos. Это руководство предназначено для разработчиков, знакомых с технологиями передачи данных HTTP, JSON, и имеющих базовые знания программирования на любом языке программирования.

Enterprise API - это интерфейс доступа к приложению, который позволяет зарегистрированным корпоративным клиентам работать с контентом и лицензиями Depositphotos. 

Для доступа к Enterprise API endpoint требуется соответствующий ключ Enterprise API. Ключ Enterprise API обеспечивает его доступ к функциональности Enterprise API, вместе с логином и паролем. Детальнее будет описано ниже.

Enterprise API включает функции для поиска файлов в базе данных Depositphotos, лицензирования контента, формирования счетов и лицензий, передачи прав (лицензий), и управление рабочими группами в рамках одной компании (или структуры компаний).

Главная информация
------------------
Регистрация
Чтобы работать с Depositphotos в качестве корпоративного клиента, вам необходима учетная запись Enterprise на Depositphotos и ключ доступа к Enterprise API (его можно получить у менеджера). 

После получения API-ключа можно приступать к работе с Enterprise API.

Общие принципы работы с Enterprise API
--------------------------------------
Все вызовы Enterprise API выполняются через HTTP-протокол с набором параметров GET или POST. Ответ API возвращает в формат JSON.

**Для всех методов API есть два обязательных параметра**

* ``dp_apikey`` - ключ API, выдаётся пользователю менеджером, после регистрации в Depositphotos
* ``dp_command`` - идентификатор метода (команды) API

Для методов, требующих управления сеансом пользователя, необходим следующий обязательный параметр:

``dp_session_id`` - уникальный идентификатор сеанса (сессии), который будет в ответе после входа

Все методы API могут возвращать два разных результата: успех (success) и ошибка (failure). 
Каждый результат success содержит параметры, специфичные для конкретного метода API, но приведенные ниже параметры являются общими для всех методов API:

* ``timestamp`` - текущая дата и время в формате «ГГГГ-ММ-ДД ЧЧ:ММ:СС»
* ``version`` - версия API, на данный момент актуальная версия 1.3
* ``type`` - успех (success) для успешных запросов, ошибка (failure), если возникают ошибка.

Пример ошибки
::

    "error": {
        "errorcode": код ошибки
        "errormsg": сообщение об ошибке
        "exception": тип ошибки
    }

**Примечания к параметрам для методов API**

bool: будет считаться TRUE для "1", FALSE для "0".
array: стандартный массив http-запроса. Например: ``param_a[]=val1&param_a[]=val2&param_b[first_key]=val3&param_b[second_key]=val4``

Аутентификация пользователя
===========================

Любые действия с файлами, а также многие другие действия требуют аутентификации пользователя.
Аутентификация пользователя возвращает уникальный идентификатор сеанса (сессии).
Идентификатор сеанса остается действительным в течение трех часов. Поэтому для обеспечения непрерывной работы необходимо настроить автоматический вход пользователя API через 1-3 часа.

loginEnterprise
~~~~~~~~~~~~~~~

Аутентификация пользователя по логину, паролю и API-ключу. Возвращает уникальный идентификатор сеанса.


Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'loginEnterprise'                                |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_login_user     | Application user login                                        |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_login_password | Application user password                                     |
+---------+-------------------+---------------------------------------------------------------+



    
Response
::

    {
        "timestamp": "2013-03-13 06:49:19",     
        "version": "1.3",
        "type": "success",
        "apiKey": "0cbc785ec3776c276bf2f",
        "sessionid": "cfaa06635e3c81b08",
        "userid": "100151"
    }


Выход из системы осуществляется с помощью запроса:

logout
~~~~~~

Завершение работы (закрыть API-сессию)

Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'logout'                                         |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                    |
+---------+-------------------+---------------------------------------------------------------+

Response
::

    {
        "timestamp": "2013-03-13 06:49:19",
        "version": "1.3",
        "type": "success",
        "apiKey": "0cbc785ec3776c276bf2f"
    }

Работа с группами пользователей
===============================

getUserEnterpriseGroup
~~~~~~~~~~~~~~~~~~~~~~

Получение информации о состоянии группы в которой состоит юзер (тип оплаты по которой работает, состояние баланса, vat и др.)

Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'getUserEnterpriseGroup'                         |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                    |
+---------+-------------------+---------------------------------------------------------------+

Response
::

    {
         "timestamp": "2013-03-13 06:49:19",
         "version": "1.3",
         "type": "success",
         "group": [
             "groupId": 12345,
             "money": 12345.12,
             "profileId": 12345,
             "isPostpayment": true,
             "balance": 12345.12,
             "vatNumber": "AX-1234",
             "vatRate": "5.5",
             "vatEnabled": true
         ]
    }



getEnterpriseUserData
~~~~~~~~~~~~~~~~~~~~~
Для получения информации профиля юзера состоящего в группе используется данный метод


Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'getEnterpriseUserData'                          |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                    |
+---------+-------------------+---------------------------------------------------------------+
| int     | dp_user_id        | Optional. Default - get info about current user               |
+---------+-------------------+---------------------------------------------------------------+

Response
::

    {
        "timestamp": "2018-05-05 14:09:44",
        "version": "1.3",
        "type": "success",
        "data": {
            "username": "Stew"
            "firstName": "Steave"
            "lastName": "Rivera"
            "city": "Futufal"
            "avatarBig":"https://static.depо.../storage/avatars/1369/1307/p_13607.jpg?15139"
            "avatarSmall":"https://static.depos.../storage/avatars/1369/13607/m_1607.jpg?15246139"
            "occupation": "Futufal"
            "avatar": "https://static.depо.../storage/avatars/1369/1307/p_13607.jpg?15139"
            "userId": "13692607"
            "address": "537 Pezis Center"
            "email": "test3@depositphotos.com"
            "phone": "+38012469843094040"
            "state": "Ghg"
            "zip": "07190"
            "registered": "1502183924"
            "enterpriseLite": {
                "groupId": 1570484
           }
            "country": "RU",
            "businessName": "Qwerty"
            "timezone": "Europe/Kiev"
            "website": "wedsite"
            "industry": "Business Services"
            "biography": "B0499144"
            "vatNumber": null
        }
    } 


Работа с файлами
================

Типы лицензий и цены на лицензирование контента зафиксированы в договоре. 

Если вы работаете по предоплате ("Prepaid"), для лицензирования контента необходим положительный баланс.
Если вы работаете с оплатой по факту лицензирования контента, в конце месяца выставляется счет на оплату за весь лицензированный контент.
 
search
~~~~~~

Поиск файлов
Метод предназначен для поиска файлов по заданным параметрам.

Required at least one of 'dp_search_query', 'dp_search_categories', 'dp_search_user', 'dp_search_username' parameters.

Request

+---------+---------------------------+---------------------------------------------------------+
| string  | dp_apikey                 || Client API key                                         |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_command                || Command name 'search'                                  |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_domain                 || Optional. Default is "depositphotos.com". Domain name  |
|         |                           || of one of CDN servers available (e.g. imgcontent.net)  |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_query           || A phrase in English, e.g. "woman hands" OR item id     |
|         |                           || which we are looking for                               |
+---------+---------------------------+------------------------------+--------------------------+
| int     | dp_search_sort            || Optional. Default is 4.     | - 1 is for 'best_match'  |
|         |                           || Definition of the sort type | - 4 is for 'best_sales'  |
|         |                           || (number from 1 to 6, each   | - 5 is for 'newest'      |
|         |                           || number equals tщ sort type) | - 6 is for 'undiscovered'|
+---------+---------------------------+------------------------------+--------------------------+
| int     | dp_search_limit           || Optional. Default is 100. A limit to the number of     |
|         |                           || returned search results                                |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_offset          || Optional. Default is 0. An offset for the first result |
|         |                           || to return, after sorting the found set.                |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_categories      || Optional. Space-delimited list of category identifiers |
|         |                           || to search for (e.g. '34 35 23')                        |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_color           || Optional. Search by the dominant color of the image.   |
|         |                           || Available colors in table below.                       |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_nudity          || Optional. Default is 0. If false, any search results   |
|         |                           || containing nudity will be hidden, otherwise all images.|
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_user            || Optional. Search by the author, using an author        |
|         |                           || identifier (e.g. 1000942)                              |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_username        || Optional. Search by username of DepositPhotos user     |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_orientation     || Optional. Search by the image orientation.             |
|         |                           || 'horizontal' or 'vertical' or 'square'                 |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_imagesize       || Optional. Search by the image size. Sets minimum image |
|         |                           || size. 's' or 'm' or 'l' or 'xl'                        |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_exclude_keyword        || Optional. Comma-separated words to exclude.            |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_photo           || Optional. Default is true. If true, the search results |
|         |                           || will include JPEG images. If false - exclude.          |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_vector          || Optional. Default is true . If true, the search results|
|         |                           || will include vector images. If false - exclude.        |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_video           || Optional. Default is false. If true, the search results|
|         |                           || will include videos. If false - exclude.               |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_editorial       || Optional. If true, the searh results will include only |
|         |                           || editorial items. If false - exclude. If not passed,    |
|         |                           || this filter won't be applied.                          |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_tracking_url           || Optional. Affiliate tracking link.                     |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_full_info              || Optional. Default is false. Return full info about     |
|         |                           || items.                                                 |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_watermark              || Optional. Watermark to use: depositphotos or neutral.  |
|         |                           || Default is neutral.                                    |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_translate_items        || Optional. Default is false. If true: title, description|
|         |                           || and categories will be translated (if translation      |
|         |                           || exists) to language dp_lang                            |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_lang                   || Optional. Default is 'en'. Language for translation    |
|         |                           || (de, ru, fr, sp, zh (for chinese) etc.)                |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_correction      || Optional. Default is '1'. Enable auto correction of    |
|         |                           || search phrase.                                         |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_height          || Optional. Minimum image height with units in           |
|         |                           || dp_search_dimension_units.                             |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_width           || Optional. Minimum image width with units in            |
|         |                           || dp_search_dimension_units.                             |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_max_height      || Optional. Maximum image height with units in           |
|         |                           || dp_search_dimension_units.                             |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_max_width       || Optional. Maximum image width with units in            |
|         |                           || dp_search_dimension_units.                             |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_dimension_units || Optional. Default = 'px'. Units for min and max image  |
|         |                           || search size. Px to inc treats as 300 DPI.              |
|         |                           || [ 'px' or 'inch' or 'cm' ]                             |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_image_url              || Optional. Search by image.                             |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_gender          || Optional. People gender 'male' or 'female' or 'both'   |
+---------+---------------------------+---------------------------------------------------------+
| bool    | dp_search_people_only     || Optional. Only people must be present                  |
+---------+---------------------------+---------------------------------------------------------+
| str|int | dp_search_age             || Optional. People age: 'infant' or 'child' or           |
|         |                           || 'teenager' or '20' or '30' or '40' or '50' or '60'     |
|         |                           || or '70'                                                |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_search_race            || Optional. People race [ 'asian' or 'brazilian' or      |
|         |                           || 'black' or 'caucasian' or 'hispanic' or 'middle' or    |
|         |                           || 'multi' or 'native' or 'other' ]                       |
+---------+---------------------------+---------------------------------------------------------+
| int     | dp_search_quantity        || Optional. People quantity in the image. Means 'any'    |
|         |                           || if greater than 3. [ 1 or 2 or 3 ]                     |
+---------+---------------------------+---------------------------------------------------------+
| string  | dp_item_permission        || Optional. Search some special type of files.           |
|         |                           || "regular" - All files (default) or                     |
|         |                           || "enterprise" - Curated Collection or                   |
|         |                           || "premium" - Focused Collection                         |
+---------+---------------------------+---------------------------------------------------------+

List of colors:

+----+---------+---------+
| 0  | any     |         |
+----+---------+---------+
| 1  | blue    | #00007c |
+----+---------+---------+
| 2  | blue    | #0005fd |
+----+---------+---------+
| 3  | blue    | #01ffff |
+----+---------+---------+
| 4  | green   | #027f00 |
+----+---------+---------+
| 5  | green   | #04fe00 |
+----+---------+---------+
| 6  | yellow  | #ffff00 |
+----+---------+---------+
| 7  | orange  | #f9be00 |
+----+---------+---------+
| 8  | orange  | #fecd9b |
+----+---------+---------+
| 9  | red     | #fe0000 |
+----+---------+---------+
| 10 | red     | #7e0004 |
+----+---------+---------+
| 11 | brown   | #653201 |
+----+---------+---------+
| 12 | violet  | #ff01ff |
+----+---------+---------+
| 13 | violet  | #810081 |
+----+---------+---------+
| 14 | grey    | #bfbfbf |
+----+---------+---------+
| 15 | grey    | #7a7a7a |
+----+---------+---------+
| 16 | black   | #000000 |
+----+---------+---------+
| 17 | white   | #ffffff |
+----+---------+---------+

Response
::

    {
        "timestamp": "2013-03-12 11:12:54",   
        "version": "1.3",                    
        "result" :[                            
        {
            "id": 3366293,                                                
            "thumbnail": "http://static4.depo...office-desk.jpg",         
            "medium_thumbnail": " http://static...positphotos_1786993-Cat.jpg
            "url": "http://s...office-desk.jpg",                         
            "url2": "http://s...api_thumb_450.jpg",                       
            "url_big": "http://s...ice-desk.jpg",                         
            "url_max_qa": "http://s...ice-desk.jpg",                      
            "itemurl": "http://depositphotos.com/...office-desk.html",    
            "mp4": "http://s...../depositphotos_1232343-item-title.mp4    
            "webm": "http://s...../depositphotos_1232343-item-title.webm  
            "published": "Jan.25, 2010 12:31:33",
            "updated": "Jan.24, 2010 02:12:29",
            "itype": "image",
            "iseditorial": false,                                         
            "title": "Pretty Caucasian business woman at office desk",   
            "description": "Pretty Caucasian ... in the back",            
            "userid": 1011061,                                            
            "username": "Alexxx"                                         
            "avatar": "",                                                
            "status": "active"                                           
            "itype": "image",                                             
            "width": 6048,                                               
            "height": 4032,                                              
            "mp": 24.385536,                                             
            "original_filesize": 2643198,                                
            "original_extension": "jpg",                                 
            "exclusive": "no",                                            
            "editorial": "no",                                            
            "deposit_item_id": 3366293
            "views": 91
            "downloads": 3
            "level": "beginner"
            "similar": [
                 1034007,
                 1235904,
                 ....
            ],
            "series" : [
                 1803161,
                 ....
            ]
            "same_model" : [
                 ....
            ]
        },
        ....
        ],
        "count":13009933,         
        "hash": 1z4ep6,           
        "type": "success"         
    }

Complimentary downloads
----------------------
Все корпоративные пользователи Enterprise API могут скачивать пробные образцы в высоком разрешении без водяных знаков (если не были установлены особые права на скачивание).

complimentaryDownload
~~~~~~~~~~~~~~~~~~~~~

Метод который возвращает ссылку на бесплатный файл для загрузки 

Request

+---------+------------------+----------------------------------------------------------------+
| string  | dp_command       | Command name 'complimentaryDownload'                           |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_apikey        | API key                                                        |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_session_id    | Session ID                                                     |
+---------+------------------+----------------------------------------------------------------+
| int     | dp_item_id       | The identifier of the item                                     |
+---------+------------------+----------------------------------------------------------------+
| int     | dp_option        | The size of file. "s-2015", "m-2015", "l-2015", etc            |          
+---------+------------------+----------------------------------------------------------------+

Параметр ``dp_option`` варьируется от коллекций и контента загрузки:
* для основной коллекции: "s-2015", "m-2015", "l-2015", "xl-2015", "vect"                                             
* для премиальной коллекции: "cs", "сl", "xl-2015"             
* для видео файлов: "240", "480", "720", "1080", "4k" 

Response
::

    {
         "timestamp": "2013-03-13 06:49:19" 
         "version": "1.3",                 
         "type": "success"                 
         "apiKey": "0cbc785ec3776c276bf2f" 
         "downloadLink": "http://st.depositphotos.com/storage/item/download?id=1234" 
    }

Для просмотра информации по бесплатным загрузкам за выбранный период используют :

getGroupCompDownloads
~~~~~~~~~~~~~~~~~~~~~
Возвращает все бесплатные загрузки для текущей группы. 

Request

+-------------+------------------+------------------------------------------------------------+
| string      | dp_command       | Command name 'getGroupCompDownloads'                       |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_apikey        | API key                                                    |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_session_id    | Session ID                                                 |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_date_start    | Start date when items were licensed                        |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_date_end      | End date when items were licensed                          |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_user_id       | Id of user licensed item                                   |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_limit         | The number of downloads                                    |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_offset        | The shift from the beginning                               |
+-------------+------------------+------------------------------------------------------------+
|string|array | dp_type          | image/vector/video                                         |
+-------------+------------------+------------------------------------------------------------+

Response
::

    {
         "timestamp": "2013-05-06 09:30:50"    
         "version": "1.3"                      
         "type": "success"                     
         "downloads": [
             {
                  "datetime": 1471871234      
                  "filename": "File name here"
                  "groupId": 12               
                  "itemId": 12345678          
                  "marker": 3                 
                  "itemType": "video"         
                  "itemLink": "/123/link.html"
                  "preview": "http://st.depositphotos.com/123/linktofile/filename.jpg"     
                  "width": 1920                
                  "height": 1080               
                  "userId": 12345678          
                  "actor": [
                      "id": 12345678          
                      "username": "Usertest"    
                  ]
                  "seler": [
                      "id": 12345678           
                      "username": "Usertest"    
                  ]
                  "download": "http://st.depositphotos.com/storage/item/download?id=1234"  
                  "visible": true             
             }
             {
                 ...
             }
         ]
         "count": 2                           
    }

Лицензирование файлов
---------------------
Лицензирование файлов - процесс, при котором клиент сообщает, что ему подходит этот файл и он оплачивает его.  Покупка файлов осуществляется за баланс или в долг для типа оплаты "Постоплата". Лицензирование происходит по одной из лицензий, которые были ранее сконфигурированы для аккаунта.Каждая лицензия имеет свою стоимость.
Для лицензирования файла необходимо знать доступный список лицензий для группы, и определить какой лицензией будет лицензирован файл.

getLicenseOfGroup
~~~~~~~~~~~~~~~~~
Возвращает список лицензий доступный для группы пользователей.

Request

+---------+------------------+----------------------------------------------------------------+
| string  | dp_command       | Command name 'getLicenseOfGroup'                               |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_apikey        | API key                                                        |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_session_id    | Session ID                                                     |
+---------+------------------+----------------------------------------------------------------+

Response
::

    {
         "timestamp": "2013-03-13 06:49:19",       
         "version": "1.3",                         
         "type": "success"                        
         "data": {                                
          [
             "licenseId": 12345                   
             "licenseName": "Digital License"     
             "templateId": 12                     
             "productType": "image"                
             "sizes": {                           
              [
                 "id": "s-2015"                   
                 "label": "Small"                 
                 "price": 12.34                   
              ],
              [
                 ...
              ]
             }
          ],
          [
             ...
          ],
         }
         "count": 2                               
    }


licenseItem
~~~~~~~~~~~

Получение лицензии на использование файла

Request

+---------+-------------------+----------------------------------------------------------------+
| string  | dp_command        | Command name 'licenseItem'                                     |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_apikey         | API key                                                        |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                     |
+---------+-------------------+----------------------------------------------------------------+
| array   | dp_licensing      | Licensing data                                                 |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_project        | Optional. Default ''                                           |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_client         | Optional. Default ''                                           |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_purchase_order | Optional. Default ''                                           |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_isbn           | Optional. Default ''                                           |
+---------+-------------------+----------------------------------------------------------------+
| string  | dp_other          | Optional. Default ''                                           |
+---------+-------------------+----------------------------------------------------------------+

Параметры dp_project,  dp_client, dp_purchase_order, dp_isbn, dp_other  являются не обязательными. Параметры настраиваются менеджером и используются для удобства.
Параметр ``dp_licensing`` должжен содержать объект с такой информацией:
::
    {
        "dp_item_id": 12345678,
        "dp_license_id": 10123,
        "dp_option": "s-2015",
        "dp_ext_options": 27
    }

Параметр ``dp_ext_options`` содержит в себе integer, с битовой маской для дополнительных опций:
 * 1 - Unlimited print
 * 2 - Multi-seat
 * 4 - Extra legal warranty
 * 8 - Product for resale and free distribution
 * 16 - Transfer rights

Response
::

    {
         "timestamp": "2013-03-13 06:49:19",          
         "version": "1.3",                            
         "type": "success"                            
         "status": "success"                          
         "result": [
             "12345678" : [                           
                 "result": "success"                  
                 "transactions": [                    
                     "12345678" : {
                         [                          
                             "sizes": 2               
                             "license": 10123         
                             "transactionId": 1234567 
                         ],
                         [
                             ...
                         ]
                     ]
                 }
                 "transaction": {                     
                     [
                         "sizes": 2                   
                         "license": 10123             
                         "transactionId": 12345678    
                     ],
                     [
                         ...
                     ]
                 }
                 "fileId": 12345678                    
                 "downloadLink": "http://st.depositphotos.com/storage/item/download?id=1234"
             ]
         ]
    }



getLicensedItems
~~~~~~~~~~~~~~~~

Метод возвращает список лицензированных файлов пользователя

Request

+---------+---------------+----------------------------------------------------------------+
| string  | dp_command    || Command name 'getLicensedItems'                               |
+---------+---------------+----------------------------------------------------------------+
| string  | dp_apikey     || API key                                                       |
+---------+---------------+----------------------------------------------------------------+
| string  | dp_session_id || Session key                                                   |
+---------+---------------+----------------------------------------------------------------+
| string  | dp_type       || Optional. Type of licenses. ["paid"|"uninvoiced"|"invoiced"]. |
|         |               || Default - all types                                           |
+---------+---------------+----------------------------------------------------------------+
| string  | dp_date_start || Optional. Start date when items were licensed                 |
+---------+---------------+----------------------------------------------------------------+
| string  | dp_date_end   || Optional. End date when items were licensed                   |
+---------+---------------+----------------------------------------------------------------+
| int     | dp_user_id    || Optional. User identity filter                                |
+---------+---------------+----------------------------------------------------------------+
| int     | dp_offset     || Optional. Shift for page                                      |
+---------+---------------+----------------------------------------------------------------+
| int     | dp_limit      || Optional. Limit for display invoices per page                 |
+---------+---------------+----------------------------------------------------------------+



Response
::

    {
         "timestamp": "2013-05-06 09:30:50"   
         "version": "1.3"                     
         "type": "success"                    
         "downloads": {
          [
             "itemTransactionId": 123456789   
             "licenseId": 10123               
             "datetime": 1471871234            
             "filename": "Some filename here" 
             "itemId": 1234567                
             "itemLink": "http://depositphotos.com/01234/item-name.html"  
             "preview": "http://static5.depositphotos.com/.../depositphotos_01234-item-name.jpg"
             "width": 110                     
             "height": 110                    
             "userId": 12345678               
             "actor": [
                 "id": 12345678               
                 "username": "Username"       
             ],
             "seller": [
                 "id": 12345678               
                 "username": "Username"       
             ]
             "licenseTransferId": 1234567     
             "status": "Invoiced"             
             "purchaseOrder": "Some order"    
             "invoice_id": 12345              
             "marker": 3                      
             "currencyId": 5                  
             "groupId": 1234                  
             "visible": true                   
          ],
          [
             ...
          ]
         }
         "count": 2
    }


getTransactionLicenseInfo
~~~~~~~~~~~~~~~~~~~~~~~~~

Метод возвращает информацию о лицензии, выданной на файл 

Request

+--------+-------------------+----------------------------------------------------------------+
| string | dp_command        | Command name 'getTransactionLicenseInfo'                       |
+--------+-------------------+----------------------------------------------------------------+
| string | dp_apikey         | API key                                                        |
+--------+-------------------+----------------------------------------------------------------+
| string | dp_session_id     | Session key                                                    |
+--------+-------------------+----------------------------------------------------------------+
| int    | dp_transaction_id | Identity of licenses transaction                               |
+--------+-------------------+----------------------------------------------------------------+

Response
::

  {
    "timestamp": "2013-03-25 10:10:06",              
    "version": "1.3",                                
    "type":"success",                                
    "license" => [
        "id" => 12                                  
        "name" => "Test"                            
        "link" => "/license-desc.html"               
        "fields" => []                              
        "transferId" => 12|null                     
    ],
    "transaction" => [
        "id" => 12                                  
        "price" => 12.99                            
        "size" => "xs|s|m|l|xl|xxl|vect|.."         
        "timestamp" => 1234567890                   
        "currencyId" => 5                           
    ],
    "item" => [
        "id" => 12                                  
        "filename" => "test.jpg"                    
        "type" => "image|vector|video"              
        "isEditorial" => true|false                 
        "isNudity" => true|false                    
        "preview" => "http://static6.depositphotos.com/..." 
        "link" => "http://test"                     
        "width" => 1080                             
        "height" => 1920                            
    ],
    "from": [                                       
        "company": "Depositphotos Inc."             
        "address": "547 Broadway, ..."              
        "someAnotherKey": anotherValue              
        ... : ...
    ]
    "to": [                                         
        "company": "Some user company"              
        "address": "Company address here"           
        "someAnotherKey": anotherValue              
        ... : ...
    ]
    "transferredTo": [                              
        "company": "Some user company"              
        "address": "Company address here"           
        "someAnotherKey": anotherValue              
        ... : ...
    ]
  }


Передача лицензии
-----------------

Передача лицензии позволяет передавать права на использование контента третьей стороне

transferEnterpriseLicense
~~~~~~~~~~~~~~~~~~~~~~~~~

Transfers license from current user to another user

Request

+-------+-------------------------+----------------------------------------------------------+
| str   | dp_command              | Command name 'transferEnterpriseLicense'                 |
+-------+-------------------------+----------------------------------------------------------+
| str   | dp_apikey               | API key                                                  |
+-------+-------------------------+----------------------------------------------------------+
| str   | dp_session_id           | Session key                                              |
+-------+-------------------------+----------------------------------------------------------+
| int   | dp_item_transaction_id  | Transaction id. Optional                                 |
+-------+-------------------------+----------------------------------------------------------+
| array | dp_item_transaction_ids | Transaction ids. Optional.                               |
+-------+-------------------------+----------------------------------------------------------+
| array | dp_from                 | From data.                                               |
+-------+-------------------------+----------------------------------------------------------+
| array | dp_to                   | To data                                                  |
+-------+-------------------------+----------------------------------------------------------+

One of ``dp_item_transaction_id`` or ``dp_item_transaction_ids`` should be passed.
``dp_from`` and ``dp_to`` data should be object, that can contain follofing keys:
::
    {
        "company":..., 
        "fullName":...,
        "address":..., 
        "city":..., 
        "state":..., 
        "zip":..., 
        "email":...,
        "phone":..., 
        "country":..., 
        "website":...
    }

Response
::

    {
         "timestamp": "2013-05-06 09:30:50"           
         "version": "1.3"                             
         "type": "success"                            
         "result": true|false                         
    }



getTransferredLicenses
~~~~~~~~~~~~~~~~~~~~~~

Returns transferred licenses for current user group

Request

+--------+---------------+------------------------------------------------------------------+
| string | dp_command    || Command name 'getTransferredLicenses'                           |
+--------+---------------+------------------------------------------------------------------+
| string | dp_apikey     || API key                                                         |
+--------+---------------+------------------------------------------------------------------+
| string | dp_session_id || Session key                                                     |
+--------+---------------+------------------------------------------------------------------+
| int    | dp_offset     || Shift for page                                                  |
+--------+---------------+------------------------------------------------------------------+
| int    | dp_limit      || Limit for display invoices per page                             |
+--------+---------------+------------------------------------------------------------------+
| string | dp_date_start || Optional. Start date when items were licensed                   |
+--------+---------------+------------------------------------------------------------------+
| string | dp_date_end   || Optional. End date when items were licensed                     |
+--------+---------------+------------------------------------------------------------------+
| int    | dp_user_id    || Optional. User identity filter                                  |
+--------+---------------+------------------------------------------------------------------+
| string | dp_type       || Optional. Type of licenses.                                     |
|        |               || Can be "paid" or "uninvoiced" or"invoiced". Default - all types |
+--------+---------------+-----------------------------------------------------------------+

Response 
::

    {
         "timestamp": "2013-05-06 09:30:50"   
         "version": "1.3"                     
         "type": "success"                    
         "downloads": {
          [
             itemTransactionId: 123456789   
             licenseId: 10123               
             datetime: 1471871234           
             filename: "Some filename here" 
             itemId: 1234567                
             itemLink: "http://depositphotos.com/01234/item-name.html"  
             preview: "http://static5.depositphotos.com/.../depositphotos_01234-item-name.jpg"
             width: 110                     
             height: 110                    
             userId: 12345678               
             actor: [
                 id: 12345678               
                 username: "Username"       
             ],
             seller: [
                 id: 12345678               
                 username: "Username"       
             ]
             licenseTransferId: 1234567     
             status: "Invoiced"             
             purchaseOrder: "Some order"    
             marker: 3                      
             currencyId: 5                  
             groupId: 1234                  
             visible: true                  
          ],
          [
             ...
          ]
         }
         count: 2
    }




Работа со счетами
-----------------

createEnterpriseInvoice
~~~~~~~~~~~~~~~~~~~~~~~

Creates invoice for licenses, created by group

Request

+--------+-------------------------+----------------------------------------------------------+
| string | dp_command              || Command name 'createEnterpriseInvoice'                  |
+--------+-------------------------+----------------------------------------------------------+ 
| string | dp_apikey               || API key                                                 |
+--------+-------------------------+----------------------------------------------------------+
| string | dp_session_id           || Session key                                             |
+--------+-------------------------+----------------------------------------------------------+       
| array  | dp_item_transaction_ids || Transaction Ids to include into the invoice as array.   |     
|        |                         || For example: {12345677, 12345678, 123456789}            |
+--------+-------------------------+----------------------------------------------------------+    
| string | dp_field_value          || Optional. Value of invoiced field to replace in the     |   
|        |                         || invoice                                                 |
+--------+-------------------------+----------------------------------------------------------+   

Response 
::

    {
         "timestamp": "2013-03-13 06:49:19",  
         "version": "1.3",                    
         "type": "success"                    
         "result": true|false                 
    }




getEnterpriseInvoice
~~~~~~~~~~~~~~~~~~~~

Returns detailed information on the invoice

Request

+--------+---------------+-------------------------------------------------------------------+
| string | dp_command    | Command name 'getEnterpriseInvoice'                               |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_apikey     | API key                                                           |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_session_id | Session key                                                       |
+--------+---------------+-------------------------------------------------------------------+
| int    | dp_invoice_id | Invoice id for detail                                             |
+--------+---------------+-------------------------------------------------------------------+


Response 
::

    {
         "timestamp": "2013-03-13 06:49:19",      
         "version": "1.3",                        
         "type": "success"                        
         "invoice": [                             
             "items" : {                          
              [                                 
                 "description": "Balance Refill"  
                 "qty": 123                       
                 "price": 123                     
              ]
              [                                 
                 "itemId": 12345678               
                 "thumbUrl": "http://static5.depositphotos.com/../depositphotos_itemname.jpg"
                 "licenseInfo": {                 
                  [
                     "key": "Project",            
                     "value": "Some project here" 
                  ],
                  [
                     "key": "Purchase Order",     
                     "value": "Some order here"    
                  ],
                  [
                     ...
                  ]
                 }
                 "licenseId": 10123               
                 "size": "s-2015"                 
                 "itemOriginalSize": [
                     "height": 1050               
                     "width": 1980                
                 ]
                 "type": "image"                  
                 "price": 123.45                  
                 "vatPrice": 134                  
                 "vatRate": 10.00                 
                 "isEditorial": false             
                 "isNudity": false                
              ],
              [
                 ...
              ]
             }
             "state": "paid"                      
             "total": 456.78                      
             "vat": 11.11                         
             "subTotal": 445.67                   
             "id": 987654                         
             "number": "ESI-10987654"             
             "type": "file_invoice"               
             "date": 1471871234                   
             "currencyId": 0                      
             "from": [                            
                 "company": "Depositphotos Inc."   
                 "address": "547 Broadway, ..."   
                 "someAnotherKey": anotherValue   
                 ... : ...
             ]
             "to": [                              
                 "company": "Some user company"   
                 "address": "Company address here"
                 "someAnotherKey": anotherValue   
                 ... : ...
             ]
             "paid": 1471871234                    
         ]
    }



getEnterpriseInvoiceCount 
~~~~~~~~~~~~~~~~~~~~~~~~~

Метод возвращает количество счетов, выставленных группе

Request

+--------+---------------+-------------------------------------------------------------------+
| string | dp_command    || Command name 'getEnterpriseInvoiceCount'                         |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_apikey     || API key                                                          |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_session_id || Session key                                                      |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_state      || Optional. Invoice state. "unpaid" or "paid" or"cancelled"        |
|        |               || Default - all                                                    |
+--------+---------------+-------------------------------------------------------------------+
| int    | dp_group_id   || Optional. Group id to filter                                     |
+--------+---------------+-------------------------------------------------------------------+


Response 
::

    {
         "timestamp": "2013-03-13 06:49:19",
         "version": "1.3",
         "type": "success",
         "count": 2          
    }


getEnterpriseInvoiceList 
~~~~~~~~~~~~~~~~~~~~~~~~~

Lists invoices of the group

Request

+--------+---------------+-------------------------------------------------------------------+
| string | dp_command    || Command name 'getEnterpriseInvoiceList'                          |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_apikey     || API key                                                          |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_session_id || Session key                                                      |
+--------+---------------+-------------------------------------------------------------------+
| int    | dp_offset     || Shift for page                                                   |
+--------+---------------+-------------------------------------------------------------------+
| int    | dp_limit      || Limit for display invoices per page                              |
+--------+---------------+-------------------------------------------------------------------+
| string | dp_state      || Optional. Invoice state. "unpaid" or "paid" or"cancelled"        |
|        |               || Default - all                                                    |
+--------+---------------+-------------------------------------------------------------------+

Response 
::

    {
         "timestamp": "2013-03-13 06:49:19",          
         "version": "1.3",                             
         "type": "success"                            
         "count": 2                                   
         "data": {
          [
             "id": 987654                             
             "date": 1471871234                       
             "description": "Invoice description here"
             "number": "ESI-10987654"                 
             "type": "file_invoice"                   
             "price": 123.45                          
             "amount": 123.45                         
             "paymentDate": 1471871234                
             "currencyId": 5                        
          ],
          [
             ...
          ]
         }
    }
