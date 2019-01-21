# Документация по vk_api
![Targets](https://camo.githubusercontent.com/d692eac339df33dd67e4e3ae1024fd0ba2077f75/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f7068702d253345253344253230372e312d3838393242462e737667) [![](https://img.shields.io/nuget/dt/VkNet.svg)](https://www.nuget.org/packages/VkNet/) [![](https://img.shields.io/github/issues/digitalstar-company/vk_api.svg)](https://github.com/digitalstar-company/vk/issues) [![](https://img.shields.io/badge/Chat-Telegram-0F80C1.svg)](https://t.me/VkDotNet)

Библиотека упрощает работу с api vk.com
> Внимание!
>* Поддержка бесед работает только если выбрана версия callBack api старше 5.8
>* Библитека адаптирована для версий 5.80 и выше
## Оглавление
1. [Подключение](#Подключение)
2. [Описание классов и их методов](#Доступные-классы)
    1. Класс [vk_api](#Класс-vk_api)
    2. Класс [group](#Класс-group)
    3. Класс [Auth](#Класс-Auth)
    4. Класс [Post](#Класс-Post)
    5. Класс [Message](#Класс-Message)
    6. Класс [VkApiException](#Исключения-VkApiException)
3. [Работа с клавиатурой](#Работа-с-клавиатурой)
3. [Файл конфигурации](#Файл-конфигурации)
4. [Примеры работы](#Примеры-работы)
***********************
## Подключение
```php
require_once "vk_api/autoload.php"; //Подключаем библиотеку

use DigitalStar\vk_api\vk_api as vk_api; // Основной класс
use DigitalStar\vk_api\group as group; // Работа с группами с ключём пользователя
use DigitalStar\vk_api\Auth as Auth; // Авторизация
use DigitalStar\vk_api\Post as Post; // Конструктор постов
use DigitalStar\vk_api\Message as Message; // Конструктор сообщений
use DigitalStar\vk_api\VkApiException as VkApiException; // Обработка ошибок
```
## Доступные классы
### Класс vk_api
#### Инициализация класса
* `$vk = new vk_api('токен группы/пользователя', 'версия api')` - авторизация через токен
* `$vk = new vk_api('логин', 'пароль', 'версия api')` - авторизация через логин/пароль пользователя
* `$vk = new vk_api('экземпляр класса Auth', 'версия api')` - использование уже готовой авторизации
#### Методы класса
* `sendMessage('id', 'message')` - отпраvkа сообщения
* `sendOK()` - отпраvkа текста 'ok', и сообщение клиенту, что-бы не ждал ответа скрипта (обход тайм-аута vk)
* [sendButton](#Работа-с-клавиатурой)`($user_id, $message, $buttons = [], $one_time = False)` - отправка клавиатуры _(только если отпровитель - бот)_
    * `$user_id` - id пользователя, которому нужно отправить клавиатуру
    * `$message` - сообщение, прикреплённое к клавиатуре
    * `$buttons` - массив клавиатуры
    * `$one_time` - исчезнет ли клавиатура после того, как пользователь ей воспользуется?
* `groupInfo($group_url)` - Возвращает информацию о группе
    * `$group_url` - ссылка на группу в любом виде или id
* `userInfo($user_url = null, $scope = [])` - Возвращает информацию о пользователе
    * `$user_url` - ссылка на пользователя в любом виде или его id
    * `$scope` - дополнительные параметры запроса к api, в формате: \['параметр' => 'значение',...]
* `request($method, $params = [])` - Отпраvkа запроса напрямую к api vk
    * `$method` - метод
    * `$params` - параметры в формате: \['параметр' => 'значение',...]
* `sendImage($id, $local_file_path)` - Отпраvkа изображения
    * `$id` - id тому, кому отправится сообщение
    * `$local_file_path` - путь до изображения
* `uploadDocsGroup($groupID, $local_file_path, $title = null)` - загрузка документа в документы сообщества
    * `$groupID` - id сообщества
    * `$local_file_path` - путь до файла
    * `$title` - название файла (если не указать, то останется локальное название)
* `uploadDocsUser($local_file_path, $title = null)` - загрузка документа в документы пользователя _(только с ключём пользователя)_
    * `$local_file_path` - путь до файла
    * `$title` - название файла (если не указать, то останется локальное название)
* `sendDocMessage($id, $local_file_path, $title = null)` - отпраvkа документа
    * `$id` - id получателя
    * `$local_file_path` - путь до файла
    * `$title` - название файла (если не указать, то останется локальное название)
* `getGroupsUser($id = [], $extended = 1, $props = [])` - возвращает информацию о группах пользователя _(только с ключём пользователя)_
    * `$id` - id пользователя, о котором надо получить информацию (если не указать, вернёт информацию о пользователе, чей токен)
    * `$extended` - (1 - подробно, 0 - нет)
    * `$props` - дополнительные параметры запроса к api, в формате: \['параметр' => 'значение',...]
* `setTryCountResendFile($var)` - задаёт максимальное количество попыток загрузки файла
    * У vk, бывает, с этим возникают некоторые баги и файл не загружается
    * `$var` - число попыток, по умолчанию 5, также можно настроить число по умолчанию в [файле конфигурации](#Файл-конфигурации)
* `setRequestIgnoreError($var)` - задаёт коды ошибок vk, при которых сообщение об ошибке игнорируется и отправляется повторный запрос
    * Внимание! запрос будет отправляться бесконечно, пока не получит от api vk ответ об успешном выполнении!
    * `$var` - массив кодов ошибок, по умолчанию [6,9,14], также можно настроить число по умолчанию в [файле конфигурации](#Файл-конфигурации)
******************
### Класс group
Класс позволяет работать с группами от имени пользователя (используя токен доступа пользователя)
#### Инициализация класса
* `$my_group = new group('id группы', 'экземпляр класса vk_api')`
#### Методы класса
Все методы [vk_api](#Класс-vk_api), которые можно использовать с ключём доступа сообщества
*******************
### Класс Auth
Класс нужен для кастомной авторизации в vk, можно задать множество параметров при авторизации и выбрать метод авторизации, также получить токен доступа или куки
#### Инициализация класса
* `$my_auth = new Auth('логин', 'пароль', $other = null, $mobile = true)` - авторизация через логин и пароль
    * Это инициализация по умолчанию при авторизации через логин/пароль в [vk_api](#Класс-vk_api)
    * $mobile - выбор метода авторизации (true - будет использоваться штатная авторизация под видом мобильного приложения, false - авторизация через приложение vk)
* `$my_auth = new Auth('куки', null, $other = null)` - авторизация через куки
    * куки - массив в json с куками
    * Работает только если куки были получены на том же сервере (с тем же ip), с которого происходит авторизация
    * Рекомендуется использовать куки, полученный при вызове метода dumpCookie()
    * Авторизацию через куки можно использовать, только если куки были получены при авторизации через приложение VK!
    
```
$other - массив для изменения значений по умолчанию
может принимать значения: ['useragent' => 'пользовательский User-Agent', 'id_app' => 'id приложения для авторизации через приложение vk'] 
Причём как что-то одно, так и все сразу
Значения по умолчанию useragent и id_app можно также изменить в файле конфигурации
```
> Как показала практика, авторизация через приложение vk может по непонятным причинам забагаваться для аккаунта, решение пока не найдено, и непонятно что на это влияет.

> Штатная авторизация под видом мобильного приложения самая надёжная и работает всегда. Рекомендуется использовать именно её
#### Методы класса
* `auth()` - запускает процесс авторизации _(ТОЛЬКО ДЛЯ АВТОРИЗАЦИИ ЧЕРЕЗ ПРИЛОЖЕНИЕ vk)_
    * После вызова метода происходит авторизация в vk (получение авторизационнах кук), но не получение токена доступа.
    (Смысл авторизации под видом мобильного приложения заключается в получении токена доступа, по этому метод auth() бессмысленен
    для метода авторизации через мобильное приложение)
* `dumpCookie()` - возвращает тикущие куки в формате JSON
* `isAuth()` - проверяет, выполнена ли авторизация _вернёт true или false_
* `getAccessToken($captcha_key = null, $captcha_sid = null)` - попытаться получить токен доступа, при успехи его и вернёт
    * Параметры не работают при авторизации через приложение VK
    * `$captcha_key` - решение каптчи
    * `$captcha_sid` - сид каптчи, полученный при ошибки ([VkApiException](#Исключения-VkApiException), можно поймать через try catch вместе со ссылкой на картинку каптчи)
    если при предедущей попытке авторизации вылетала каптча
**************
### Класс Post
Этот класс является удобным конструктором запросов для создания и публикации постов
#### Инициализация класса
* `$new_post = new Post('экземпляр класса vk_api')`
#### Методы класса
* `setMessage($message)` - задаёт текст
* `addImage($images1, $images2,...)` - добавляет картинки во вложения
    * Может принимать как 1 параметр (массив из ссылок на файлы), так и 1 или больше отдельных параметров (строк - ссылок на файлы)
* `addProp($prop, $value)` - задаёт дополнительный, пользовательский, параметр в запросе к api
    * `$prop` - название параметра
    * `$value` - значение
* `addDocs($docs, $title = null)` - добавляет документы во вложения
    * Может работать двумя способами:
        * Принимать `$docs` - путь до файла и `$title` - новое название файла, если нужно изменить
        * Принимать массив: `[['path' => 'путь', 'title' => 'название'], ['path' => 'путь', 'title' => null],...]`
* `removeImages($images)` - удаляет из вложений картинку с путём `$images`
* `removeDocs($docs)` - удаляет из вложений документ с путём `$docs`
* `removeProp($prop)` - удаляет пользовательский параметр `$prop`
* `getMedia()` - возвращает весь массив вложений
* `getMessage()` - возвращает заданное сообщение
* `getProps()` - возвращает пользовательские параметры
* `send($id, $publish_date = null)` - публикует пост, возвращает id поста после публикации
    * `$id` - id пользователя или сообщества (если сообщества, то с минусом), на стену которого будет опубликован пост
    * `$publish_date` - датта в формате Unixtime, когда будет опубликована запись (опубликуется в отложенные до этого времени).
    Если не задать, опубликуетс сразу же
> Максимальное количество вложений - 10. Это ограничение самого VK, при превышении сгенерируется исключение

> Также метод send() можно вызывать сколько угодно раз с разными параметрами, для публикации одного и того же поста в разных местах

> При вызове нескольких подряд методов с префиксом add или remove, они будут дополнять друг друга, а не заменять
******************
### Класс Message
Этот класс является удобным конструктором запросов для создания и отпраvkи сообщений
#### Инициализация класса
* `$new_message = new Message($vk)`
    * `$vk` - экземпляр класса vk_api или group, при инициализации в группе с ключём пользователя
#### Методы класса
Все те же, что и у класса [Post](#Класс-Post), только `send()` слегка другой и есть дополнительные
* [setKeyboard](#Работа-с-клавиатурой)`($keyboard = [], $one_time = false)` - прикрепляет клавиатуру к сообщению _(только если отпровитель - бот)_
    * `$buttons` - массив клавиатуры
    * `$one_time` - исчезнет ли клавиатура после того, как пользователь ей воспользуется?
    * В целом, работает так же, как и `sendButton()`
* `getKeyboard()` - возвращает настройки прикреплённой клавиатуры
* `send($id)` - отправляет сообщение
    * `$id` - $id адресата сообщения
****************
## Исключения VkApiException
```php
try {

} catch (VkApiException $e) {
    $e->getMessage() // сообщение ошибки
}
```
> Исключение генерируется, если VK возвращает в ответ ошибку (возвращается весь JSON вывода), и в некоторых случаях библиотека
сама генерирует исключение.
*******************
## Работа с клавиатурой
### Создание кнопок:
```php
$button1_1 = [null, "white", "white"];
$button1_2 = [["animals" => 'Pig'], "blue", "blue"];
$button2_1 = [["animals" => 'Cow'], "green", "green"];
$button2_2 = [["animals" => 'Chicken'], "red", "red"];
```
#### Как описываются кнопки ?
Параметр 1: Payload - может принимать значение: ассоциативный массив или null\
Параметр 2: Надпись на кнопке - текст\
Параметр 3: Цвет кнопки - может принимать значения: white, blue, green, red
### Отпраvkа клавиатуры:
```php
$id // ID пользователя, кому будет отправлена клавиатура, или peer_id беседы
$message // Сообщение, отправляемое вместе с клавиатурой
$buttons = [[$button, ...], ...] // Массив из отправляемый кнопок
$one_time // Не обязательный параметр. Принимает значение True или False. Если True - после нажатия клавиши клавиатуры, клавиатура исчезнет, Flase - не исчезнет. По умолчанию = False
$vk->sendButton($id, $message, $buttons, $one_time);
```
#### Пример, отпраvkа клавиатуры с текстом "Клавиатура":
```php
$id // ID пользователя, кому будет отправлена клавиатура
[[$button, ...], ...] // Массив из отправляемый кнопок
$one_time // Не обязательный параметр. Принимает значение True или False. Если True - после нажатия клавиши клавиатуры, клавиатура исчезнет, Flase - не исчезнет. По умолчанию = False
$vk->sendButton($id, 'Клавиатура', [
	[$button1_1, $button1_2],
	[$button2_1, $button2_2]
], $one_time);
```
Кнопки будут выглядеть так:
```
[ white ] [  blue ]
[ green ] [  red  ]
```
Такой запрос:
```php
$vk->sendButton($id, 'Клавиатура', [
	[$button1_1, $button1_2, $button2_2],
	[$button2_1]
]);
```
Выведет следующие кнопки:
```
[ white ] [  blue ] [  red  ]
[           green           ]
```
### Удаление кнопок (клавиатуры из диалога):
Обращаем ваше внимание, что если передать параметр $one_time = True (см. отпраvkа клавиатуры), клавиатура исчезнет после нажатия на одну из кнопок.\
Для того, что-бы вручную выключить клавиатуру, нужно выполнить следующий запрос:
```php
$id // ID пользователя
$message // Сообщение, отправляемое при удалении клавиатуры
$vk->sendButton($id, $message);
```
*******************
## Файл конфигурации
Файл конфигурации называется `config.php` и находится в папке vk_api\
Пользовательские параметры файла:
```php
    // массив кодов ошибок vk, при которых сообщение об ошибке игнорируется и отправляется повторный запрос к api
const REQUEST_IGNORE_ERROR = [6,9,14];
    // максимальное количество попыток загрузки файла
const COUNT_TRY_SEND_FILE = 5;

    // Auth
    // Запрашиваемые права доступа для токена пользователя по уполчанию
const DEFAULT_SCOPE = "notify,friends,photos,audio,video,stories,pages,status,notes,messages,wall,ads,offline,docs,groups,notifications,stats,email,market";
    // User-Agent по умолчанию
const DEFAULT_USERAGENT = 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.86 Safari/537.36';
    // ID приложения vk по умолчанию
const DEFAULT_ID_APP = '6660888';    
```
*******************************
## Примеры работы
Файлы с некоторыми примерами работы библиотеки лежат в корне проекта

## План развития проекта
В процессе заполнения

## Помощь проекту
- Яндекс.Деньги - <money.yandex.ru/to/410014638432302>
- Дебетовая карта - 2202201272652211
- Также вы можете помочь проекту `Pull Request`'ом
