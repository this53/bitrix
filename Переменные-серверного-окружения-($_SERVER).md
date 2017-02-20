```php
<?php
use \Bitrix\Main\Application;
$context = Application::getInstance()->getContext();
$server = $context->getServer();
$server->getDocumentRoot(); //document root
$server->getPersonalRoot(); //bitrix folder 
$server->getHttpHost(); // $_SERVER["HTTP_HOST"]
$server->getServerName(); // $_SERVER["SERVER_NAME"] 
$server->getServerAddr(); // $_SERVER["SERVER_ADDR"] 
$server->getServerPort(); // $_SERVER["SERVER_PORT"] 
$server->getRequestUri(); // $_SERVER["REQUEST_URI"] 
$server->getRequestMethod(); // $_SERVER["REQUEST_METHOD"] 
$server->getPhpSelf(); // $_SERVER["PHP_SELF"] 
$server->getScriptName(); // $_SERVER["SCRIPT_NAME"] 
```