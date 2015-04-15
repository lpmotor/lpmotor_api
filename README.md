# Описание API для LPmotor.ru

Все запросы к API требуют указания ключа, который идентифицирует пользователя (user_key).
Чтобы получить этот ключ, нужно зарегистрировать пользователя с помощью метода __/user/signup__, который вернет сгенерированный user_key.

API возвращает результат выполнения операции в JSON-формате.

Возможны следующие коды HTTP-статуса:

* 200 : запрос успешно выполнен
* 400 : неверный запрос (ошибка в переданных данных)
* 403 : доступ запрещен
* 404 : метод API не найден
* 500 : ошибка на сервере

.

## 1. Регистрация пользователя

### /user/signup

Регистрирует нового пользователя в системе.

Запрос нужно отправлять на сервер авторизации: _https://auth.lpmotor.ru_.

Входные параметры (все обязательны):

1. __partner_key__ : ключ партнера, 32 символа (его генерируем мы, LPmotor)
1. __email__ : правильный почтовый ящик, который указывает ваш пользователь при регистрации

Выходные параметры:

1. __status__ : статус выполнения операции
1. __user_key__ : ключ, идентицифирующий пользователя в системе

-

Пример:

    https://auth.lpmotor.ru/api/v1/auth/signup?partner_key=9d4f&email=test@mail.ru

Результат выполнения:

    {"status":"ok", "user_key":"9d4f"}

-

Возможные ошибки:

* __1111__ : Пользователь с заданным E-mail уже зарегистрирован;
* __2222__ : Партнер с заданным partner_key не найден;