---
sidebar_position: 4
---

# Canonical для фильтра и пагинации

```php title="init.php"
<?
use \Bitrix\Main\Loader;
use \Bitrix\Main\EventManager;

$eventManager = EventManager::getInstance();

//Canonical для фильтра и пагинации
$eventManager->addEventHandler('main', 'OnEpilog', function() {
	global $APPLICATION;

	if (Loader::includeSharewareModule('rabbit.urltosef') && \CRabbitUrlToSEF::isHasSEF())
	{
		$requestUrl = $_SERVER['REQUEST_URI_REAL'];
	}
	else
	{
		$requestUrl = $_SERVER['REQUEST_URI'];
	}
	
	$masUrl = explode('/', $requestUrl);

	if (in_array('filter', $masUrl))
	{
		$masUrlArr = explode('filter', $requestUrl);
		//Asset::getInstance()->addString('<link href="https://'.$_SERVER['HTTP_HOST'].$masUrlArr[0].'" rel="canonical" />', true);
		$APPLICATION->SetPageProperty('canonical', 'https://'.$_SERVER['HTTP_HOST'].$masUrlArr[0]);
	}
	else if ($_GET['PAGEN_1'])
	{
		//Asset::getInstance()->addString('<link href="https://'.$_SERVER['HTTP_HOST'].parse_url($requestUrl)['path'].'" rel="canonical" />', true);
		$APPLICATION->SetPageProperty('canonical', 'https://'.$_SERVER['HTTP_HOST'].parse_url($requestUrl)['path']);
	}
});
```