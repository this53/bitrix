```php
<?php
use Bitrix\Highloadblock as HL;
$hlblock   = HL\HighloadBlockTable::getById( # )->fetch();
$entity   = HL\HighloadBlockTable::compileEntity( $hlblock ); //генерация класса 
$entityClass = $entity->getDataClass();

class MyDomainObjectTable extends #entityClass# {
…//наша бизнес логика проекта
} 
```