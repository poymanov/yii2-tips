# Создание копии существующей модели

Иногда возникает необходимость сделать копию модели (**```ActiveRecord```** или обычной модели).
 
Наиболее **правильный и элегантный метод** это сделать выглядит следующим образом:
 
```php
$model = ExampleOne::findOne(['id' => 1]);
$modelCopy = new ExampleOne();
$modelCopy->setAttributes($model->attributes);
$modelCopy->save();
``` 
 
В первой строке мы каким-либо образом получаем **нужную нам модель**. В данном случае это **```ActiveRecord```**. Мы можем найти её в записях базы или получить из какого-то другого метода:

```php
$model = ExampleOne::findOne(['id' => 1]);
```

Затем создаем **пустую копию** идентичного по классу объекта:

```php
$modelCopy = new ExampleOne();
```

Общий для всех моделей метод **```setAttributes```** может получить для себя массив вида:

```php
[
    'атрибут' => 'значение атрибута'
]
```

И записать его содержимое в качестве **атрибутов и значений** в модель.

Свойство, также общее для всех моделей, **```attributes```** возвращает такой же **массив** атрибутов и их значений для **модели**, из которой был вызван.

Оба этих метода используются в следующей строке и получается что мы **записываем атрибуты** существующей модели в новую копию:

```php
$modelCopy->setAttributes($model->attributes);
```

После этого, по необходимости записываем модель (только **```ActiveRecord```**):

```php
$modelCopy->save();
```

Обратите внимание, что модель при сохранении может **не пройти** процедуру валидации. 

Причиной этому может быть использование в копии модели значений, которые согласно правилам валидации модели **являются уникальными**:

```php
class ExampleOne extends ActiveRecord() {
    public function rules()
    {
        return [
            ...
            ['name', 'unique']
            ...
        ];
    }
}
```

В этом случае модель **не будет** записана.

После копирования атрибутов в новую копию объекта **допускается изменять** значения атрибутов:

```php
$modelCopy->name = 'New name';
```