# Безопасное хранение данных подключения к БД и параметров в базовом шаблоне приложения

В [продвинутом шаблоне](https://github.com/yiisoft/yii2-app-advanced) приложения Yii2 предусмотрено безопасное хранение данных подключения к БД и параметров с помощью локальных файлов **```*.local.php```**. Подобная маска добавляется в файл **```.gitignore```** и конфидициальные данные не попадают в систему контроля версий при следующей синхронизации.
 
В базовом шаблоне для обеспечения подобного эффекта необходимо вручную реализовать этот принцип хранения файлов.

Интересующие нас файлы:

```php
/config/db.php
/config/params.php
```

В первом хранятся данные для подключения к **базе данных**.
Во втором могут находится данные, которые, возможно, будут различаться в разных местах публикации приложения. Например, **ключи для счетчиков** систем аналитики, или **контактные данные** администратора сайта.

В той же директории **```/config/```** нужно создать файлы:

```php
db.local.php
params.local.php
.gitignore
```

Чтобы не наполнять лишней информацией глобальный файл **```.gitignore```**, мы создали в директории **```/config/```** свой. Его содержимое будет следующим:

```php
*.local.php
```

Эта означает, что все файлы в директории **```/config/```**, которые имеют в своем наименовании **```.local.php```** не будут отслеживаться в системах контроля версий. Другими словами, в них мы можем писать любые данные, не боясь что они будут видны другим пользователям/участникам команды. 

Теперь обратим внимание на файлы:

```php
/config/web.php
/config/console.php
```

Это файлы, в которых хранится конфигурация нашего приложения. Для web-реализации и консоли. Добавим в каждый из них простую функцию:

```php
function merge_configs($base, $customized)
{
    $baseConfig = require($base);
    if (is_file($customized))
        return yii\helpers\ArrayHelper::merge($baseConfig, require($customized));
    return $baseConfig;
}
```

А также две строки кода:

```php
$params = merge_configs(__DIR__ . '/params.php', __DIR__ . '/params.local.php');
$db = merge_configs(__DIR__ . '/db.php', __DIR__ . '/db.local.php');
```

Функция получает на вход **два параметра** - пути к файлам, значения которых **необходимо объединить**. Т.к. информация о подключении к БД и параметрах хранится в виде массива, вышеуказанная функция просто **объединит два массива**, замещая данные из первого файла вторым. В двух строчках кода мы передаем пути к файлам с информацией о **подключении к БД** и **параметрах** (основным и нашим локальным). На выходе получаем **общий параметр** в виде массива с учетом данных основного и локального файлов.

Теперь их необходимо подключить в наши конфигурационные файлы.

В **```/config/web.php```** и **```/config/console.php```**:

```php
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'components' => [
        ...
        'db' => $db,
        ...
    ],
    'params' => $params,
];

```


Кроме этого в файле **```/config/web.php```** есть строка конфигуации:

```php
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'components' => [
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => 'yYF7r018Zftww5LawafwGhQ3T2ivoU3T',
        ],
        ...
    ],
];
```

Её я, на всякий случай, рекомендую вынести в **```/config/params.local.php```**:

```php
return [
    'cookieValidationKey' => 'yYF7r018Zftww5LawafwGhQ3T2ivoU3T'
]
```

А в **```/config/web.php```** заменить следующим значением:

```php
$config = [
    'id' => 'basic',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'components' => [
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => 'params.cookieValidationKey',
        ],
        ...
    ],
];
```

Внутренний механизм Yii поймет, что это значение нужно взять из массива данных файлов **```params.php```** и **```params.local.php```**.

В **```/config/console.php```** использовать настройку **```cookieValidationKey```** не нужно.

Несмотря на то что мы создали локальные версии файлов **```db.php```** и **```params.php```**, **не нужно удалять** эти файлы. Они должны **храниться** вместе с вашим приложением в **системе контроля версий** и служить примером того, как требуется заполнять их **локальные копии**. Это может быть полезно для **других разработчиков**, которые захотят развернуть ваше приложение. Естественно, об этом нужно нужно упомянуть в **инструкции** к вашему приложению.