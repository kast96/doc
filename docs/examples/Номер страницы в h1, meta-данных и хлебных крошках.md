---
sidebar_position: 3
---

# Номер страницы в h1, meta-данных и хлебных крошках

```php title="init.php"
<?
use \Bitrix\Main\Loader;
use \Bitrix\Main\EventManager;

$eventManager = EventManager::getInstance();

//Кастомный GetPagePath для seo чпу (для хлебных крошек)
function getPagePathCustom($page = false, $get_index_page = null)
{
	$query = ($_GET['PAGEN_1']) ? '?PAGEN_1='.$_GET['PAGEN_1'] : '';
	if (Loader::includeSharewareModule('rabbit.urltosef') && \CRabbitUrlToSEF::isHasSEF())
	{
		return parse_url($_SERVER['REQUEST_URI_REAL'])['path'] . $query;
	}
	else
	{
		return GetPagePath($page, $get_index_page) . $query;
	}
}

//Добавление номера страницы в тайтл и хлебные крошки
$eventManager->addEventHandler('main', 'OnEpilog', function()
{
	global $APPLICATION;

	$curPage = $APPLICATION->GetCurPage(false);
	$urlPaths = explode('/', $curPage);

	if ($_GET['PAGEN_1'])
	{
		$APPLICATION->AddChainItem('Страница ' . $_GET['PAGEN_1'], getPagePathCustom());
		$APPLICATION->SetPageProperty('title', $APPLICATION->GetPageProperty('title') . ' - страница ' . $_GET['PAGEN_1']);
		$APPLICATION->SetPageProperty('description', $APPLICATION->GetPageProperty('description') . ' - страница ' . $_GET['PAGEN_1']);
		$APPLICATION->SetTitle($APPLICATION->GetTitle() . ' - страница ' . $_GET['PAGEN_1']);
	}
}, false, 500);
```