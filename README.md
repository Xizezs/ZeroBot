# Документация по ZeroBot
Библиотека по созданию ботов, упрощает вам жизнь, вы можете очень быстро и легко сделать из вашего сообщества бота.
ВК группы со списком обновлений: https://vk.com/zero_vk

## Оглавление
* [Установка и Подключение](#Установка)
* [Методы и Классы](#Методы)
* [Кнопки](#Кнопки)

## Установка
> 1) Скачиваем файлы
> 2) Перекидываем их в одну папку и выдаем права 777 на все файлы.
> 3) Настраиваем bot.php, events это список того на что будет отвечать бот, например юзера кикнули или он сам ушел и будет происходить действие. Токен, думаю и так все знают что это такое.
```php 
require("lib/Zero.php");

$token = ''; //Токен Сообщества

//Список эвентов таких как получения сообщения, отписка и подписка и тд.

$events = [
    'message_new' => function($object, $Zero){ //Действие выполняется при получение сообщения
        global $cmds; //Получаем список комманд
        foreach ($cmds as $cmd){
            if(preg_match_all($cmd['r'], $object['text'], $params, PREG_SET_ORDER)){ //Ищем команду в сообщение пользователя
                $cmd['f']($Zero, $Zero->user(), $params[0]); //Выполняем функцию если команда найдена
            }
        }
    }
];

$Zero = new Zero($token);
$Zero->longpoll->init();
```
> 4) Папка cmds, в ней один файл: Test.php. Их можно самим создавать, они автоматически будут подключатся, и добавляем команду по шаблону. В один файл тоже можно добавлять команды.
```php
CommandManager::addCommand('/^kek$/iu', function ($Zero, $user, $params){
    $Zero->reply($user['first_name'].", Cheburek");
});
```
> 5) Запускаем: Переходим в директорию с ботом и пишем php bot.php

## Методы
### Сообщения
>* Отправка сообщений
```php 
$Zero->send('Сообщение');
```
>* Пересылка сообщений
```php
$Zero->reply('Сообщение/Ответ');
```
### Данные о человеке
```php
$user = $Zero->user(); //id пользователя берется автоматически, ничего указывать не надо
$user['first_name']; //Имя
$user['last_name']; //Фамилия
$user['id']; //Ид юзера
```
## Кнопки
Что-бы сделать кнопку, мы зайдем в наш файл с командой, например cmds/Test.php
> Список цветов кнопок
>* COLOR_RED - Красный
>* COLOR_BLUE - Синий
>* COLOR_WHITE - Белый
>* COLOR_GREEN - Зеленый

> Типы кнопок
>* $button->btn($payload=цифра, COLOR_ЦВЕТ, $text); - Обычная кнопка
>* $button->btn_app($payload, $app_id, $owner_id, $text) - Запуск приложения группы
>* $button->btn_loc($payload) - Местоположение пользователя
>* $button->btn_pay($payload, $hash) - Отправка денег
>* $button->btn_clear() - Очистка кнопок, если one_time=false
>* Подробнее можно прочитать здесь: https://vk.com/dev/bots_docs_3
```php
CommandManager::addCommand('/^Test$/iu', function ($Zero, $user, $params){
    global $button; //Берем переменную с классом
    $button->btn_send('Нажми на кнопку', true, [ 
        [$button->btn(0, 'Кнопочка', COLOR_RED)]
    ]);
});
```
> (Нажми на кнопочку) - Это текст который отправится вместе с кнопкой/кнопками.
> (true) - Это one_time, если сделать true, то кнопки уберутся после первого нажатия, false - можно будет бесконечно жать кнопки и они не исчезнут после нажатий.
> (0) - PayLoad, это ид кнопки. Задаем любую цифру.
> (COLOR_RED) - Цвет.

### Работа кнопок
Как создавать одну или несколько кнопок в ряд.
>* Создание несколько в один ряд
```php
CommandManager::addCommand('/^Test$/iu', function ($Zero, $user, $params){
    global $button; //Берем переменную с классом
    $button->btn_send('Нажми на кнопку', true, [ 
        [
             $button->btn(0, 'Кнопочка', COLOR_RED),
             $button->btn(1, 'Кнопочка 2', COLOR_BLUE)
        ]
    ]);
});
```
>* Кнопки по рядам
```php
CommandManager::addCommand('/^Test$/iu', function ($Zero, $user, $params){
    global $button; //Берем переменную с классом
    $button->btn_send('Нажми на кнопку', true, [ 
        [ $button->btn(0, 'Кнопочка', COLOR_RED) ],
        [ $button->btn(1, 'Кнопочка 2', COLOR_BLUE) ]
    ]);
});
```
>* А если несколько кнопок в первом ряду, и одна в другом
```php
CommandManager::addCommand('/^Test$/iu', function ($Zero, $user, $params){
    global $button; //Берем переменную с классом
    $button->btn_send('Нажми на кнопку', true, [ 
        [ $button->btn(0, 'Кнопочка', COLOR_RED), $button->getbtn(1, 'Кнопочка 2', COLOR_BLUE) ],
        [ $button->btn(3, 'Кнопочка 3', COLOR_BLUE) ]
    ]);
});
```
>* Очистка кнопок без one_time
```php
CommandManager::addCommand('/^Test$/iu', function ($Zero, $user, $params){
    global $button; //Берем переменную с классом
    $button->btn_clear();
});
```
