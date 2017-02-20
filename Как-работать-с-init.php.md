## app.php
```php
<?php
class App{
  public function run(){
    //Consts
    require_once (__DIR__ . '/lib/consts.php');
    //Autoload
    require_once (__DIR__ . '/autoload.php');
    //Helpers
    require_once (__DIR__ . '/lib/helpers.php');
    //Events
    require_once (__DIR__ . '/lib/events/page.php');
    require_once (__DIR__ . '/lib/events/feedback.php');
    require_once (__DIR__ . '/lib/events/review.php');
  }
}
```

## init.php
```php
<?php
/*Please no code in this file. Structure your local folder*/
//Consts
require_once (__DIR__ . '/lib/consts.php');
//Autoload
require_once (__DIR__ . '/autoload.php');
//Helpers
require_once (__DIR__ . '/lib/helpers.php');
//Events
require_once (__DIR__ . '/lib/events/page.php');
require_once (__DIR__ . '/lib/events/feedback.php');
require_once (__DIR__ . '/lib/events/review.php');
```

## init2.php
```php
<?php 
/*Please no code in this file. Structure your local folder*/
require_once 'lib/app.php';
$app = new App();
$app->run();
```
