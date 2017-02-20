```php
<?php
use \Bitrix\Main\Application;
$context = Application::getInstance()->getContext();
$request = $context->getRequest();
//get value
$value = $request->get("some_name");
$value = $request["some_name"];
$value = $request->getQuery("some_name");   // get GET-value
$value = $request->getPost("some_name");   // get POST-value
$value = $request->getFile("some_name");   // get posted file
$value = $request->getCookie("some_name");   // get cookie value
$uri = $request->getRequestUri();   // get uri
$method = $request->getRequestMethod();   // get request method
$flag = $request->isPost();      // true - POST-request, otherwise false
$flag = $request->isGet();      // true - GET-request, otherwise false
```