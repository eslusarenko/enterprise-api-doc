.. figure:: _static/depositphotos_logo.png

*************************************
Depositphotos Enterprise Solution API
*************************************

`Enterprise Solution <https://depositphotos.com/enterprise.html>`_

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
::

"error": {
	"errorcode": код ошибки
	"errormsg": сообщение об ошибке
	"exception": тип ошибки
}

**Примечания к параметрам для методов API**

bool: будет считаться TRUE для «1», «true», «on» и «yes». FALSE в противном случае.
массив: стандартный массив http-запроса (например, ``param_a[]=val1&param_a[]=val2&param_b[first_key]=val3&param_b[second_key]=val4``).

Аутентификация пользователя
===========================

Любые действия с файлами, так же как и многие другие действия требуют аутентификации пользователя.
Аутентификация пользователя возвращает  уникальный идентификатор сеанса (сессии).
Идентификатор сеанса остается действительным в течение трех часов. Поэтому для обеспечения непрерывной работы необходимо настроить автоматический вход пользователя API через один-три часа.

loginEnterprise
~~~~~~~~~~~~~~~

Аутентификация пользователя по логину, паролю и API-ключу. Возвращает уникальный идентификатор сеанса.

Request
::

	param string dp_command 			Command name 'loginEnterprise'
	param string dp_apikey 				API key
	param string dp_login_user 			Application user login
	param string dp_login_password 		Application user password

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
::

param string dp_command    		Command name 'logout'
param string dp_apikey 			API key
param string dp_session_id 		Session ID

Response
::

{
	timestamp: "2013-03-13 06:49:19",
version: "1.3",
	type: "success",
	apiKey: "0cbc785ec3776c276bf2f"
 }

Работа с группами пользователей
===============================






Работа с файлами
================

Типы лицензий и цены на лицензирование контента зафиксированы в договоре. 

Если вы работаете по предоплате ("Prepaid"), для лицензирования контента необходим положительный баланс.
Если вы работает по пост-оплате, в конце месяца выставляется счет на оплату за весь лицензированный контент.
 
search
~~~~~~
Поиск файлов
Метод предназначен для поиска файлов по заданным параметрам.

Required at least one of 'dp_search_query', 'dp_search_categories', 'dp_search_user', 'dp_search_username' parameters.

Request
::

param string dp_apikey 	Client API key
param string dp_command 	Command name 'search'
param string dp_domain 	Optional. Default is "depositphotos.com". Domain name of one of CDN servers available (e.g. imgcontent.net)
param string dp_search_query 	A phrase in English, e.g. "woman hands" OR item id which we are looking for
param  int dp_search_sort 	Optional. Default is 4. Definition of the sort type (number from 1 to 6, each number equals sort type). In fact, 2nd an 3rd search type are equal 4th now ("best_sales"). So you should use 1, 4, 5 and 6 sort type.
# 1 is for 'best_match'
# 4 is for 'best_sales'
# 5 is for 'newest'
# 6 is for 'undiscovered'

param int dp_search_limit 	Optional. Default is 100. A limit to the number of returned search results
param string dp_search_offset 	Optional. Default is 0. An offset for the first result to return, after sorting the found set.
param string dp_search_categories	Optional. Space-delimited list of category identifiers to search for (e.g. '34 35 23')
param int dp_search_color 	Optional. Search by the dominant color of the image. List of colors:
# 0 - any
# 1 - blue, #00007c
# 2 - blue, #0005fd
# 3 - blue, #01ffff
# 4 - green, #027f00
# 5 - green, #04fe00
# 6 - yellow, #ffff00
# 7 - orange, #f9be00
# 8 - orange, #fecd9b
# 9 - red, #fe0000
# 10 - red, #7e0004
# 11 - brown, #653201
# 12 - violet, #ff01ff
# 13 - violet, #810081
# 14 - grey, #bfbfbf
# 15 - grey, #7a7a7a
# 16 - black, #000000
# 17 - white, #ffffff
param bool dp_search_nudity 	Optional. Default is 0. If false, any search results containing nudity will be hidden, otherwise all images.
param int dp_search_user 	Optional. Search by the author, using an author identifier (e.g. 1000942)
param string dp_search_username 	Optional. Search by username of DepositPhotos user
param string dp_search_orientation 	Optional. Search by the image orientation. [ 'horizontal' | 'vertical' | 'square' ]
param string dp_search_imagesize 	Optional. Search by the image size. Sets minimum image size. [ 's' | 'm' | 'l' | 'xl' ]
param string $dp_exclude_keyword 	Optional. Comma-separated words to exclude.
param bool dp_search_photo 	Optional. Default is true. If true, the search results will include JPEG images. If false - exclude.
param bool dp_search_vector 	Optional. Default is true . If true, the search results will include vector images. If false - exclude.
param bool dp_search_video 	Optional. Default is false. If true, the search results will include videos. If false - exclude.
param bool dp_search_editorial 	Optional. If true, the searh results will include only editorial items. If false - exclude. If not passed, this filter won't be applied.
param string dp_tracking_url 	Optional. Affiliate tracking link.
param bool dp_full_info 	Optional. Default is false. Return full info about items.
param string dp_watermark 	Optional. Watermark to use: depositphotos | neutral. Default is neutral.
param string dp_translate_items 	Optional. Default is false. If true - title, description and categories will be translated (if translation exists) to language dp_lang
param string dp_lang 	Optional. Default is 'en'. Language for translation (de, ru, fr, sp, zh (for chinese) etc.)
param bool dp_search_correction 	Optional. Default is '1'. Enable auto correction of search phrase.
param int dp_search_height 	Optional. Minimum image height with units in dp_search_dimension_units.
param int dp_search_width 	Optional. Minimum image width with units in dp_search_dimension_units.
param int dp_search_max_height 	Optional. Maximum image height with units in dp_search_dimension_units.
param int dp_search_max_width 	Optional. Maximum image width with units in dp_search_dimension_units.
param string dp_search_dimension_units 	Optional. Default = 'px'. Units for min and max image search size. Px to inc treats as 300 DPI. [ 'px' | 'inch' | 'cm' ]
param string dp_image_url 	Optional. Search by image.
param string dp_search_gender 	Optional. People gender [ 'male' | 'female' | 'both' ]
param bool dp_search_people_only 	Optional. Only people must be present
param str|int dp_search_age 	Optional. People age [ 'infant' | 'child' | 'teenager' | '20' | '30' | '40' | '50' | '60' | '70' ]
param string dp_search_race 	Optional. People race [ 'asian' | 'brazilian' | 'black' | 'caucasian' | 'hispanic' | 'middle' | 'multi' | 'native' | 'other' ]
param int dp_search_quantity 	Optional. People quantity in the image. Means 'any' if greater than 3. [ 1 | 2 | 3 ]
param string dp_item_permission 	Optional. Search some special type of files. [ "regular" - All files (default) | "enterprise" - Curated Collection (For ES users only)  | "premium" - Focused Collection (For ES users only) )

Response
::

{
 timestamp: "2013-03-12 11:12:54",   
 version: "1.3",                    
 result:[                            
 {
     id: 3366293,                                                
     thumbnail: "http://static4.depo...office-desk.jpg",         
     medium_thumbnail: " http://static...positphotos_1786993-Cat.jpg
     url: "http://s...office-desk.jpg",                         
     url2: "http://s...api_thumb_450.jpg",                       
     url_big: "http://s...ice-desk.jpg",                         
     url_max_qa: "http://s...ice-desk.jpg",                      
     itemurl: "http://depositphotos.com/...office-desk.html",    
     mp4: "http://s...../depositphotos_1232343-item-title.mp4    
     webm: "http://s...../depositphotos_1232343-item-title.webm  
     published: "Jan.25, 2010 12:31:33",
     updated: "Jan.24, 2010 02:12:29",
     itype: "image",
     iseditorial: false,                                         
     title: "Pretty Caucasian business woman at office desk",   
     description: "Pretty Caucasian ... in the back",            
     userid: 1011061,                                            
     username: "Alexxx"                                         
     avatar: "",                                                
     status: "active"                                           
     itype: "image",                                             
     width: 6048,                                               
     height: 4032,                                              
     mp: 24.385536,                                             
     original_filesize: 2643198,                                
     original_extension: "jpg",                                 
     exclusive: "no",                                            
     editorial: "no",                                            
     deposit_item_id: 3366293
     views: 91
     downloads: 3
     level: "beginner"
     similar: [
             1034007,
             1235904,
             ....
     ],
     series : [
             1803161,
             ....
     ]
     same_model : [
             ....
     ]
 },
 ....
 ],
 count:13009933,         
 hash: 1z4ep6,           
 type: "success"         
}



Complimentary downloads
----------------------
Все пользователи внутри Enterprise решения позволяет скачивать пробные образцы в высоком разрешении без водяных знаков.

complimentaryDownload
~~~~~~~~~~~~~~~~~~~~~

Метод который возвращает ссылку на бесплатный файл для загрузки 

Request
::

param string  dp_command 		Command name 'complimentaryDownload'
param string  dp_apikey  		API key
param string  dp_session_id  	Session ID
param int dp_item_id  		The identifier of the item
param int dp_option   		The size of file. ["s-2015"|"m-2015"|"l-2015"|"xl-2015"| ... ]

dp_option для основной коллекции: "s-2015", "m-2015", "l-2015", "xl-2015", "vect"
dp_option для премиальной коллекции: "cs", "сl", "xl-2015"
dp_option для видео файлов: "240", "480", "720", "1080", "4k"


Response
::

{
     timestamp: "2013-03-13 06:49:19" 
     version: "1.3",                 
     type: "success"                 
     apiKey: "0cbc785ec3776c276bf2f" 
     downloadLink: "http://st.depositphotos.com/storage/item/download?id=1234" 
}


getGroupCompDownloads
~~~~~~~~~~~~~~~~~~~~~

Возвращает все бесплатные загрузки для текущей группы 

Request
::

param string dp_command 		Command name 'getGroupCompDownloads'
param string dp_apikey 		API key
param string dp_session_id 	Session key
param string dp_date_start 	Start date when items were licensed
param string dp_date_end 		End date when items were licensed 
param string dp_user_id 		id of user licensed item
param int dp_limit
param int dp_offset
param string|array dp_type 	image/vector/video

Response
::

{
     timestamp: "2013-05-06 09:30:50"    
     version: "1.3"                      
     type: "success"                     
     downloads: [
         {
              datetime: 1471871234      
              filename: "File name here"
              groupId: 12               
              itemId: 12345678          
              marker: 3                 
              itemType: "video"         
              itemLink: "/123/link.html"
              preview: "http://st.depositphotos.com/123/linktofile/filename.jpg"     
              width: 1920                
              height: 1080               
              userId: 12345678          
              actor: [
                  id: 12345678          
                  username: Usertest    
              ]
              seler: [
                  id: 12345678           
                  username: Usertest    
              ]
              download: "http://st.depositphotos.com/storage/item/download?id=1234"  
              visible: true             
         }
         {
             ...
         }
     ]
     count: 2                           
}




Table of Contents
-----------------

.. toctree::