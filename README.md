# Описание API для LPmotor.ru

Запросы к API выполняются в следующем формате:

    <адрес сервера>/api/v1/<метод>?<параметры>

(где `v1` указывает версию API)

API возвращает результат выполнения операции в JSON-формате.

Возможны следующие коды HTTP-статуса:

* `200` : запрос успешно выполнен
* `400` : неверный запрос (ошибка в переданных данных)
    * проверьте формат и набор входящих параметров
* `403` : доступ запрещен
    * проверьте правильность переданного ключа
    * возможно, передано неверное сочетание user_key и id объекта
* `404` : метод API не найден
    * проверьте правильность написания метода API и его версии
* `406` : сработали limit-ы на количество запросов
    * нужно повторить запрос через некоторое время
* `500` : ошибка на сервере
    * напишите нам, с указанием ошибки

Все запросы к API требуют указания ключа, который идентифицирует пользователя (user_key).
Чтобы получить этот ключ, нужно зарегистрировать пользователя с помощью метода [/user/signup](#user_reg), который вернет сгенерированный user_key.

### Список методов API

1. [Регистрация пользователя](#user_reg)
1. [Получение списка сайтов](#site_list)
1. [Создание сайта](#site_create)

.

## <a name="servers"></a>0. Сервера для запросов API

##### <a name="srv_auth"></a>Сервер авторизации

Имеет адрес _https://auth.lpmotor.ru_ и нужен только для регистрации нового пользователя.

-

##### <a name="srv_data"></a>Сервер данных

Сервер данных содержит все объекты, которые пользователь создает в сервисе (сайты, страницы и тд).

Адрес сервера имеет вид _https://adm99999.lpmotor.ru_, где `99999` нужно заменить на идентификатор сервера `adm_id`.
Этот идентификатор выдается при регистрации каждому пользователю.

.

## <a name="user_reg"></a>1. Регистрация пользователя

    GET /user/signup

Регистрирует нового пользователя в системе.

Запрос нужно отправлять на [сервер авторизации](#srv_auth).

Входные параметры (все обязательны):

* __partner_key__ (hash) ключ партнера, 32 символа (генерируется в LPmotor)
* __email__ (string) почтовый ящик, который указывает ваш пользователь при регистрации

Выходные параметры:

* __status__ (string) статус выполнения операции
* __user_key__ (hash) ключ, идентицифирующий пользователя в системе
* __adm_id__ (int) идентификатор сервера, к которому нужно делать запросы, относящиеся к конкретному пользователю

-

__Пример запроса__:

    https://auth.lpmotor.ru/api/v1/user/signup?email=test@mail.ru&partner_key=1ef26d1dc263168ae4814f5aaaa9ce8f

Результат выполнения:

    HTTP/1.1 200 OK
    {"status":"ok", "user_key":"ae4814f5aaaa9ce8f1ef26d1dc263168", "adm_id":"1"}

-

Возможные коды ошибок:

* `1265` : Не передан параметр partner_key;
* `1266` : Неверный формат partner_key;
* `1267` : Не передан параметр email;
* `1268` : Неверный формат email;
* `1270` : Пользователь с переданным E-mail уже зарегистрирован;
* `1273` : partner_key не зарегистрирован в нашей системе;

.

## <a name="site_list"></a>2. Получение списка сайтов

    GET /site/list

Возвращает данные о всех сайтах пользователя.

Запрос нужно отправлять на [сервер данных](#srv_data).

-

##### Запрос

* __обязательные__
    * _user_key_ - (hash) идентификатор пользователя
* необязательные
    * _status_ - (int) id [статуса публикации сайта](#site_status)

-

##### Ответ

* __status__ - (string) статус выполнения операции
* __list__ - (array) массив данных о сайтах

Каждый элемент массива __list__ имеет формат:

    {
        "id": 999, //идентификатор сайта
        "name": "Мой сайт", //название сайта
        "status": "1" //id статуса публикации
    }

-

##### <a name="site_status"></a>Статус публикации может иметь значения:

* `0` - _draft_ (черновик),
* `1` - _published_ (опубликован),
* `2` - _deleted_ (удалён)

-

##### Пример ---

    https://adm1.lpmotor.ru/api/v1/site/list?user_key=ae4814f5aaaa9ce8f1ef26d1dc263168

Ответ сервера:

    HTTP/1.1 200 OK
    {
        "status":"ok",
        "list":[
            {
                "id": 28,
                "name": "Сайт 1",
                "status": "0"
            },
            {
                "id": 342,
                "name": "Сайт 2",
                "status": "1"
            }
        ]
    }

.

## <a name="site_create"></a>3. Создание сайта

    GET /site/create

Создает сайт для заданного пользователя.

Запрос нужно отправлять на [сервер данных](#srv_data).

-

##### Запрос

* __обязательно__
    * _user_key_ - (hash) идентификатор пользователя
* необязательно
    * _name_ - (string) название сайта

-

##### Ответ

* __status__ - (string) статус выполнения операции
* __site_id__ - (int) id нового сайта

-

##### Пример ---

    https://adm1.lpmotor.ru/api/v1/site/create?name=Сайт1&user_key=ae4814f5aaaa9ce8f1ef26d1dc263168

Ответ сервера:

    HTTP/1.1 200 OK
    {
        "status":"ok",
        "site_id": 32,
    }

.