```php
<?php 
use Bitrix\Main\Application;
$connection = Application::getConnection();
/*sql requests will be run*/
$connection->startTracker(false);
// code any queries
$connection->stopTracker();
$sql = $rsData->getTrackerQuery()->getSql();
/*sql requests will not  be run*/
// stop queries 
$connection->disableQueryExecuting();
// code any queries
// runq ueries
$connection->enableQueryExecuting();
/*all disabled queries */
$queries = $connection->getDisabledQueryExecutingDump();
```

```php
$sqlTracker = $connection->getTracker();
$sqlTracker->getTime(); //Время выполнения запросов
$sqlTracker->getCounter(); //Кол-во запросов
$sqlTracker->getQueries(); //Запросы
```