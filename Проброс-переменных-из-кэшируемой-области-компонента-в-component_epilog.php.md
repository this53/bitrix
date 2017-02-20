## result_modifier.php
```php
<?php
$cp = $this->__component;
if (is_object($cp))
{
  $cp->arResult["SOME_KEY"] = $arResult["SOME_KEY"];
  $cp->SetResultCacheKeys(array("SOME_KEY")); //cache keys in $arResult array
}
```