## CRUD
```php
<?php
use \Bitrix\Main\Application;
use  \Bitrix\Main\Type\DateTime as Dtime;

//Get values
$context = Application::getInstance()->getContext();
$request = $context->getRequest();
$int = $request->get('int');

//add
\My\Module\MyTable::add([
    'INT' => $int,
    'TIME' => new \Bitrix\Main\Type\DateTime(null,0)
]);

//update
$result = \My\Module\MyTable::update($id, [
    'TIME' => new Dtime('2002-11-15 00:00:00', 'Y-m-d H:i:s')
]);

//get
$filter = [
  '><TIME' => [
    new Dtime(
      date($request->get('date').' 00:00:00'),
      'Y-m-d H:i:s'
    ),
    new Dtime(
      date($request->get('date').' 23:59:59'),
      'Y-m-d H:i:s'
    )
]];
$dataObj = \My\Module\MyTable::getList([
    'select' => ["INT", "TIME"],
    'order' => ["TIME" => 'asc'],
    'filter' => $filter,
]);
while ($data = $dataObj->fetch(new  \My\Module\TestConverter))
{
    $result[] = $data;
}

//delete
$result = \My\Module\MyTable::delete($id);



//get errors
$result = \My\Module\MyTable::update(...);

if (!$result->isSuccess())
{
    $errors = $result->getErrorMessages();
}
```

## Get List
```php
<?php

BookTable::getList(array(
    'select'  => ... // имена полей, которые необходимо получить в результате
    'filter'  => ... // описание фильтра для WHERE и HAVING
    'group'   => ... // явное указание полей, по которым нужно группировать результат
    'order'   => ... // параметры сортировки
    'limit'   => ... // количество записей
    'offset'  => ... // смещение для limit
    'runtime' => ... // динамически определенные поля
));

//fetchall();
$result = BookTable::getList($parameters);
$rows = $result->fetchAll();

$rows = BookTable::getList($parameters)->fetchAll();

//
BookTable::getList(array(
    'select' => array('CNT'),
    'runtime' => array(
        new Entity\ExpressionField('CNT', 'COUNT(*)')
    )
));

//
BookTable::getById(1);
BookTable::getByPrimary(array('ID' => 1));

// такие вызовы будут аналогичны следующему вызову getList:
BookTable::getList(array(
    'filter' => array('=ID' => 1)
));

//
$row = BookTable::getRowById($id);

// аналогичный результат можно получить так:
$result = BookTable::getById($id);
$row = $result->fetch();

// или так
$result = BookTable::getList(array(
    'filter' => array('=ID' => 1)
));

$row = $result->fetch();

//
$row = BookTable::getRow(array(
    'filter' => array('%=TITLE' => 'Patterns%'),
    'order' => array('ID')
));

// аналогичный результат можно получить так:
$result = BookTable::getList(array(
    'filter' => array('%=TITLE' => 'Patterns%'),
    'order' => array('ID')
    'limit' => 1
));

$row = $result->fetch();

//// аналогично через Entity\Query
$q = new Entity\Query(BookTable::getEntity());
$q->setSelect(array('ISBN', 'TITLE', 'PUBLISH_DATE'));
$q->setFilter(array('=ID' => 1));
$result = $q->exec();
```