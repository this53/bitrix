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