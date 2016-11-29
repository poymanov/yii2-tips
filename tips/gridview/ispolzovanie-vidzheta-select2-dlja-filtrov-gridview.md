# Использование виджета Select2 для фильтров GridView

Для вывода данных [моделей](https://github.com/yiisoft/yii2/blob/master/docs/guide-ru/structure-models.md) в Yii2 часто используется виджет [GridView](https://nix-tips.ru/yii2-razbiraemsya-s-gridview.html). Используя этот виджет можно реализовать фильтры для отбора полей по некоторым значениями. Очень удобно для какого-то поля сформировать список доступных значений для фильтрации и представить их в виде списка выбора (aka html select). 

Виджет [Select2](https://github.com/kartik-v/yii2-widget-select2) позволяет создавать более продвинутые выпадающие списки.

Помимо применения этого виджета в обычных представлениях, его можно интегрировать в виджет **GridView**.

Ниже показан пример кода, когда мы хотим сделать возможность не только делать фильтацию по определенным вариантам из списка, но и иметь возможность искать нужные значение среди представленных в списке (по вводу начальных символов или всего слова целиком).

Это код необходимо использовать для значения ```columns``` из параметров виджета **GridView**.

```php
[
   'attribute' => 'status',
   'format' => 'html',
   'filter' => Select2::widget([
       'model' => $filterModel,
       'attribute' => 'status',
       'data' => $filterModel->getStatusLabels(),
       'theme' => Select2::THEME_BOOTSTRAP,
       'options' => [
           'prompt' => '',
       ],
       'pluginOptions' => [
           'allowClear' => true,
           'width' => '170px',
       ],
   ]),
   'value' => function ($item, $params) {
       return "<span title=\"Status: {$item->status}\">{$item->getStatusLabel()}</span>";
   }
]
```
Это **SearchModel** для модели, данные которой необходимо отобразить в **GridView**.

```php
$filterModel
```
Это метод модели, который возвращает **массив**, который будем использован для формирования **списка вариантов выбора** фильтра.

```php
$filterModel->getStatusLabels()
```

