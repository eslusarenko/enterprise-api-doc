.. figure:: _static/depositphotos_logo.png

*************************************
Depositphotos Enterprise Solution API
*************************************

`Enterprise Solution <https://depositphotos.com/enterprise.html>`_

.. contents:: Table of Contents

Introduction
============

These guidelines describe the use of API for working with Depositphotos files. These guidelines are intended for developers familiar with HTTP and JSON data transfer technologies and possessing basic knowledge of programming in any of programming languages.

Enterprise API is the application access interface, which allows registered corporate clients to work with Depositphotos’ content and licenses. 

To get access to Enterprise API endpoint, you need a corresponding Enterprise API key. Along with login and password, Enterprise API key provides access to Enterprise API features. Details are given below.

Enterprise API includes features for searching files in the Depositphotos database, for content licensing, issuing of invoices and licensing, transfer of rights (licences), and management of working groups within one company (or corporate structure.)


General information
===================

Registration
------------

In order to work with Depositphotos as a corporate client, you need an Enterprise account at Depositphotos and an access key to Enterprise API (you can get it from your manager.)

After obtaining username, password and the API key, you can start working with Enterprise API.


General principles for working with Enterprise API
--------------------------------------------------

All Enterprise API calls are performed through HTTPS protocol with GET or POST parameters set. API returns a response in JSON format.
All requests must be sent to the following base url: ``https://api.depositphotos.com``

There are two mandatory parameters for all API methods

:dp_apikey: is an API key, which is issued by a manager to a user after his/her registration on Depositphotos
:dp_command: API method (command) ID

For methods, which require user authorization, there is one more mandatory parameter required:

:dp_session_id: unique session ID, which will be in response after logging in

For requests you can use either form-data or json format for all variables. But if you want to use JSON format, you should:

* set the Content-Type header to application/json
* pass dp_apikey and dp_command parameters in the URL (i.e. ``https://api.depositphotos.com?dp_apikey=123&dp_command=loginEnterprise``)

Example of request with JSON format:
::

    curl --silent -H 'content-type: application/json' \
    'https://api.depositphotos.com?dp_command=loginEnterprise&dp_apikey=e0a1bdd9923bc601293cd053ffa4eaf6e0a1bdd1' \
    -d '{"dp_login_user": "something.similar.to.your.email", "dp_login_password": "yourSecurePassword"}'

Example of request with form-data format:
::

    curl --silent https://api.depositphotos.com \
    -d 'dp_command=loginEnterprise&dp_apikey=e0a1bdd9923bc601293cd053ffa4eaf6e0a1bdd1&dp_login_user=your.login&dp_login_password=123456'

One more example. The request with form-data format and dp_command/dp_apikey sent in the URL:
::

    curl --silent 'https://api.depositphotos.com?dp_command=loginEnterprise&dp_apikey=e0a1bdd9923bc601293cd053ffa4eaf6e0a1bdd1' \
    -d 'dp_login_user=your.login&dp_login_password=123456'

All API methods can return two different results: success or failure (stated in the ‘type’ variable of response).
You should rely on the ‘type’ variable to determine the result of the request, not on response HTTP code.

Each ‘success’ result contains specific parameters for each API method, but following parameters are general for all API methods:

:timestamp: current date and time in the "YYYY-MM-DD HH:MM:SS" format
:version: API version; the most up-to-date version is 1.3
:type: 'success' for successful requests, ‘failure’ if an error occurs

Example of an error
::

    "error": {
        "errorcode": error code
        "errormsg": error message
        "exception": error type
    }

.. admonition:: Notes on parameters for API methods

   - bool: will be considered TRUE for "1", FALSE for "0".
   - array: standard array of http-request. Example: ``param_a[]=val1&param_a[]=val2&param_b[first_key]=val3&param_b[second_key]=val4``

User authentication
-------------------

Any actions with files, as well as many other actions require user authentication.
User authentication returns a unique session ID.
Session ID remains valid for three hours. So, to provide uninterrupted work, it’s necessary to set up automated API user login in 1-3 hours.

loginEnterprise
^^^^^^^^^^^^^^^

User authentication with a login, password, and API key. Returns a unique session ID


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
    
.. code-block:: json
    :caption: Response

    {
        "timestamp": "2013-03-13 06:49:19",     
        "version": "1.3",
        "type": "success",
        "apiKey": "0cbc785ec3776c276bf2f",
        "sessionid": "cfaa06635e3c81b08",
        "userid": "100151"
    }


Logout is performed using the request:

logout
^^^^^^

Logout (close API session)


Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'logout'                                         |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                    |
+---------+-------------------+---------------------------------------------------------------+

.. code-block:: json
    :caption: Response

    {
        "timestamp": "2013-03-13 06:49:19",
        "version": "1.3",
        "type": "success",
        "apiKey": "0cbc785ec3776c276bf2f"
    }

Working with user groups
------------------------

getUserEnterpriseGroup
^^^^^^^^^^^^^^^^^^^^^^

Getting information on the group the user belongs to (type of payment, balance, vat, etc.)


Request

+---------+-------------------+---------------------------------------------------------------+
| string  | dp_command        | Command name 'getUserEnterpriseGroup'                         |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_apikey         | API key                                                       |
+---------+-------------------+---------------------------------------------------------------+
| string  | dp_session_id     | Session ID                                                    |
+---------+-------------------+---------------------------------------------------------------+

.. code-block:: json
    :caption: Response

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
^^^^^^^^^^^^^^^^^^^^^

This method is used to get account data of the user belonging to the group


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

.. code-block:: json
    :caption: Response

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


Working with files
------------------

License types and content licensing prices are stipulated in the agreement. 

If you work on a prepaid basis ("Prepaid"), you need positive account balance to license the content.
If you work with on a postpaid basis, an invoice for all licensed content is issued in the end of the month.


search
^^^^^^

Content search
The method is designed to search content files by given parameters.

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
|         |                           || number equals to sort type) | - 6 is for 'undiscovered'|
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
| bool    | dp_search_editorial       || Optional. If true, the search results will include only|
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

List of languages:

 * ``en`` - English
 * ``de`` - Deutsch
 * ``fr`` - Français
 * ``sp`` - Español
 * ``ru`` - Русский
 * ``it`` - Italiano
 * ``pt`` - Português
 * ``es`` - Español
 * ``pl`` - Polski
 * ``nl`` - Nederlands
 * ``jp`` - 日本語
 * ``cz`` - Česky
 * ``se`` - Svenska
 * ``zh`` - 中文
 * ``tr`` - Türkçe
 * ``mx`` - Español (Mexico)
 * ``gr`` - Ελληνικά
 * ``ko`` - 한국어
 * ``br`` - Português (Brasil)
 * ``hu`` - Magyar
 * ``uk`` - Українська
 * ``ro`` - Română
 * ``id`` - Bahasa Indonesia
 * ``th`` - ไทย

.. code-block:: json
    :caption: Response

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
-----------------------

All corporate users of Enterprise API can download high-resolution test samples without watermarks (unless special downloading rights are set.)


complimentaryDownload
^^^^^^^^^^^^^^^^^^^^^

Returns a link to a file for free download  

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

``dp_option`` parameter varies based on collections and downloaded content:

* for main collection: "s-2015", "m-2015", "l-2015", "xl-2015", "vect"                                             
* for premium collection: "cs", "сl", "xl-2015"             
* for video files: "240", "480", "720", "1080", "4k" 

.. code-block:: json
    :caption: Response

    {
         "timestamp": "2013-03-13 06:49:19" 
         "version": "1.3",                 
         "type": "success"                 
         "apiKey": "0cbc785ec3776c276bf2f" 
         "downloadLink": "http://st.depositphotos.com/storage/item/download?id=1234" 
    }

To view information on free downloads over a selected period, the following method is used:

getGroupCompDownloads
^^^^^^^^^^^^^^^^^^^^^

Returns all free downloads for a current group. 

Request

+-------------+------------------+------------------------------------------------------------+
| string      | dp_command       | Command name 'getGroupCompDownloads'                       |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_apikey        | API key                                                    |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_session_id    | Session ID                                                 |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_date_start    | Start date when items were downloaded                      |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_date_end      | End date when items were downloaded                        |
+-------------+------------------+------------------------------------------------------------+
| string      | dp_user_id       | Id of user downloaded item                                 |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_limit         | The number of downloads                                    |
+-------------+------------------+------------------------------------------------------------+
| int         | dp_offset        | The shift from the beginning                               |
+-------------+------------------+------------------------------------------------------------+
|string|array | dp_type          | image/vector/video                                         |
+-------------+------------------+------------------------------------------------------------+

.. code-block:: json
    :caption: Response

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

Licensing of files
------------------

Licensing of files is the process when a client informs that the file suits him/her and then he/she pays for it.  Files purchase is performed using account balance or on credit in case of “Postpaid” payment. Licensing is performed under one of the licenses previously configured for the account. Each license has its own price.
To license the file, you have to know the list of available licenses for the group and to determine the license, which will be used for licensing of the file.

getLicenseOfGroup
^^^^^^^^^^^^^^^^^

Returns a list of licenses available to the group of users.

Request

+---------+------------------+----------------------------------------------------------------+
| string  | dp_command       | Command name 'getLicenseOfGroup'                               |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_apikey        | API key                                                        |
+---------+------------------+----------------------------------------------------------------+
| string  | dp_session_id    | Session ID                                                     |
+---------+------------------+----------------------------------------------------------------+

.. code-block:: json
    :caption: Response

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
^^^^^^^^^^^

Obtaining a license to use a file

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

Parameters ``dp_project``,  ``dp_client``, ``dp_purchase_order``, ``dp_isbn``, ``dp_other``  are non-mandatory. These parameters are set up by the manager and are used for convenience.
``dp_licensing`` parameter must contain an object with the following data:
::
    {
        "dp_item_id": 12345678,
        "dp_license_id": 10123,
        "dp_option": "s-2015",
        "dp_ext_options": 27
    }

``dp_ext_options`` parameter contains integer with a bit mask for additional options:

+----+------------------------------------------+
| 1  | Unlimited print                          |
+----+------------------------------------------+
| 2  | Multi-seat                               |
+----+------------------------------------------+
| 4  | Extra legal warranty                     |
+----+------------------------------------------+
| 8  | Product for resale and free distribution |
+----+------------------------------------------+
| 16 | Transfer rights                          |
+----+------------------------------------------+

.. code-block:: json
    :caption: Response

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
^^^^^^^^^^^^^^^^

This method returns a list of user’s licensed files

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

.. code-block:: json
    :caption: Response

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
^^^^^^^^^^^^^^^^^^^^^^^^^

The method returns information on the license issued on a file 

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

.. code-block:: json
    :caption: Response

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


Transfer of license
-------------------

Transfer of license allows transferring rights to use the content to the third party

transferEnterpriseLicense
^^^^^^^^^^^^^^^^^^^^^^^^^

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
``dp_from`` and ``dp_to`` data should be object, that can contain following keys:
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

.. code-block:: json
    :caption: Response

    {
         "timestamp": "2013-05-06 09:30:50"           
         "version": "1.3"                             
         "type": "success"                            
         "result": true|false                         
    }



getTransferredLicenses
^^^^^^^^^^^^^^^^^^^^^^

Returns transferred licenses for the current user group

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
|        |               || Can be "paid" or "uninvoiced" or "invoiced".Default - all types |
+--------+---------------+------------------------------------------------------------------+


.. code-block:: json
    :caption: Response

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




Working with invoices
---------------------

createEnterpriseInvoice
^^^^^^^^^^^^^^^^^^^^^^^

Creates an invoice for licenses created by the group

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

.. code-block:: json
    :caption: Response

    {
         "timestamp": "2013-03-13 06:49:19",  
         "version": "1.3",                    
         "type": "success"                    
         "result": true|false                 
    }




getEnterpriseInvoice
^^^^^^^^^^^^^^^^^^^^

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


.. code-block:: json
    :caption: Response

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
^^^^^^^^^^^^^^^^^^^^^^^^^

Returns number of invoices issued to the group

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


.. code-block:: json
    :caption: Response

    {
         "timestamp": "2013-03-13 06:49:19",
         "version": "1.3",
         "type": "success",
         "count": 2          
    }


getEnterpriseInvoiceList 
^^^^^^^^^^^^^^^^^^^^^^^^

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

.. code-block:: json
    :caption: Response

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

