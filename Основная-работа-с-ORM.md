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

## References
```php
<?php
namespace SomePartner\MyBooksCatalog;

use Bitrix\Main\Entity;

class AuthorTable extends Entity\DataManager
{
    public static function getTableName()
    {
        return 'my_book_author';
    }

    public static function getMap()
    {
        return array(
            new Entity\IntegerField('ID', array(
                'primary' => true,
                'autocomplete' => true
            )),
            new Entity\StringField('NAME'),
            new Entity\StringField('LAST_NAME')
        );
    }
}

class BookTable extends Entity\DataManager
{
    ...
    public static function getMap()
    {
        return array(
            ...
            new Entity\IntegerField('AUTHOR_ID'),
            new Entity\ReferenceField(
                'AUTHOR',
                'SomePartner\MyBooksCatalog\Author',
                array('=this.AUTHOR_ID' => 'ref.ID'),
            )
        );
    }
    ...
}

BookTable::getList(array(
    'select' => array('TITLE', 'AUTHOR.NAME', 'AUTHOR.LAST_NAME')
));

$author_type = 5;

new Entity\ReferenceField(
    'AUTHOR',
    'SomePartner\MyBooksCatalog\Author',
    array(
        '=this.AUTHOR_ID' => 'ref.ID',
        '=ref.TYPE' => new DB\SqlExpression('?i', $author_type)
    )
)

\SomePartner\MyBooksCatalog\AuthorTable::getList(array(
    'select' => array(
        'NAME',
        'LAST_NAME',
        'BOOK_TITLE' => '\SomePartner\MyBooksCatalog\BookTable:AUTHOR.TITLE'
    )
));
```

## Table
```php
<?php
//module (my.module)
namespace My\Module;

    use Bitrix\Main\Entity;
    use Bitrix\Main\Localization\Loc;
    Loc::loadMessages(__FILE__);


    class MyTable extends Entity\DataManager
    {
        public static function getFilePath()
        {
            return __FILE__;
        }

        public static function getTableName()
        {
            return 'my_module_table';
        }
        
        public static function onBeforeAdd(Entity\Event $event)
        {
            $result = new Entity\EventResult;
            $data = $event->getParameter("fields");
    
            if (isset($data['ISBN']))
            {
                $cleanIsbn = str_replace('-', '', $data['ISBN']);
                $result->modifyFields(array('ISBN' => $cleanIsbn));
            }
    
            return $result;
        }
        
        public static function onBeforeUpdate(Entity\Event $event)
        {
           $result = new Entity\EventResult;
           $data = $event->getParameter("fields");
        
           if (isset($data['ISBN']))
           {
              $result->unsetFields(array('ISBN'));
           }
        
           return $result;
           
           //or
           $result = new Entity\EventResult;
            $data = $event->getParameter("fields");
        
            if (isset($data['ISBN']))
            {
                $result->addError(new Entity\FieldError(
                    $event->getEntity()->getField('ISBN'),
                    'Запрещено менять ISBN код у существующих книг'
                ));
            }
        
            return $result;
        }

        public static function getMap()
        {
            return [
                'INT' => [
                    'data_type' => 'integer',
                    'primary' => true,
                    'required' => true,
                    'title' => Loc::getMessage('MESSAGE_INT'),
                ],
                'TIME' => [
                    'data_type' => 'datetime',
                    'title' => Loc::getMessage('MESSAGE_TIME'),
                ],
            ];
                            
            //or
            return [
                new Entity\IntegerField('INT'),
                //new Entity\StringField('ISBN'),
                //new Entity\StringField('TITLE'),
                //new Entity\DateField('TIME')
                new Entity\DateTimeField('TIME')
            ];
            
            //or
            return [
                new Entity\BooleanField('NAME', [
                    'values' => ['N', 'Y']
                ]),
                new Entity\EnumField('NAME', [
                    'values' => ['VALUE1', 'VALUE2', 'VALUE3']
                ])
            ];
            
            //or
            return [
                new Entity\IntegerField('ID', [
                    'primary' => true, //Primary & autoincrement & required
                    'autocomplete' => true
                ])    
            ];
            
            //or
            return [
                new Entity\IntegerField('INT', [
                   'required' => true,
                   'column_name' => 'ISBNCODE'
                ]),
                new Entity\ExpressionField('AGE_DAYS',
                    'DATEDIFF(NOW(), %s)', ['PUBLISH_DATE']
                )
            ];
            
            //or
            return [
                new Entity\DateField('PUBLISH_DATE', [
                    'default_value' => new Type\Date
                ])
            ];
            
            //or
            return [
                new Entity\DateField('PUBLISH_DATE', array(
                    'default_value' => function () {
                        // figure out last friday date
                        $lastFriday = date('Y-m-d', strtotime('last friday'));
                        return new Type\Date($lastFriday, 'Y-m-d');
                    }
                ))
            ];
            
            //or
            return [
                new Entity\StringField('ISBN', [
                    'required' => true,
                    'column_name' => 'ISBNCODE',
                    'validation' => function($value, $primary, $row, $field) {
                        return [
                            new Entity\Validator\RegExp('/[\d-]{13,}/')
                        ];
                    }
                ])
            ];
            
            //or
            return [
                new Entity\TextField('EDITIONS_ISBN', [
                    'save_data_modification' => function () {
                        return [
                            function ($value) {
                                return serialize($value);
                            }
                        ];
                    },
                    'fetch_data_modification' => function () {
                        return [
                            function ($value) {
                                return unserialize($value);
                            }
                        ];
                    }
                ])
            ];
            
            //or
            return [
                new Entity\TextField('EDITIONS_ISBN', [
                    'serialized' => true
                ])
            ];
        }
    }
```

## Test Converter
```php
<?php
namespace My\Module;
use \Bitrix\Main\Text\Converter;
use \Bitrix\Main\Type\DateTime as DTime;
use \Bitrix\Main\Text\String as Str;

class TestConverter extends Converter
{
    public function encode($text, $textType = "")
    {
        if ($text instanceof DTime)
            return $text->format('Y-m-d H:i:s');

        return Str::htmlEncode($text);
    }

    public function decode($text, $textType = "")
    {
        if (is_object($text))
            return $text;

        return Str::htmlDecode($text);
    }
}
```

## UF Fields
```php
<?php
class BookTable extends Entity\DataManager
{
    public static function getUfId()
    {
        return 'MY_BOOK';
    }
}
```