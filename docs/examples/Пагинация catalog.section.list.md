---
sidebar_position: 1
---

# Пагинация catalog.section.list

## Подготовка компонента

В компоненте catalog.section.list нужно отключить кеш и добавить параметры количества разделов на странице `PAGE_SECTION_COUNT` и опцию отобрать ли пагинацию внизу страницы `DISPLAY_BOTTOM_PAGER`

```php title="catalog.section.list"
<?$APPLICATION->IncludeComponent(
	"bitrix:catalog.section.list",
	"main",
	array(
		...
		"CACHE_TYPE" => 'N',
		"PAGE_SECTION_COUNT" => 20,
		"DISPLAY_BOTTOM_PAGER" => 'Y',
	),
	false
);?>
```


## Логика внутри компонента

С помощью класса `CDBResult` создаем новый объект результата для массива разделов `$arResult['SECTIONS']` и задаем ему пагинацию.

```php title="template.php"
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
$this->setFrameMode(true);?>

<?
/*
Отключаем хранение пагинации в сессии для текущей страницы
*/
CPageOption::SetOptionString("main", "nav_page_in_session", "N");

/*
С помощью класса `CDBResult` создаем новый объект результата
для массива разделов `$arResult['SECTIONS']` и задаем ему пагинацию
*/
$rsNav = new CDBResult;
$rsNav->InitFromArray($arResult['SECTIONS']);
$rsNav->NavStart(intval($arParams['PAGE_SECTION_COUNT']) ?: 20);

/*
Если доступна навигация, перезаписываем массив $arResult['SECTIONS']
только разделами текущей страницы. Если разделов меньше чем максимально
доступное количество на странице, метод IsNavPrint вернет false.
И все что внутри не выполнется.
По этому массив $arResult['SECTIONS'] перезаписывается,
а не выпоняется вывод разделов внутри while
*/
if ($rsNav->IsNavPrint())
{
	$arSections = array();
	while ($arSection = $rsNav->Fetch())
	{
		$arSections[] = $arSection;
	}
	$arResult['SECTIONS'] = $arSections;
}
?>

<?
/*
Выводим разделы
*/
?>
<?if ($arResult['SECTIONS']):?>
	<?foreach ($arResult['SECTIONS'] as $arSection):?>
		<div><?=$arSection['NAME']?></div>
	<?endforeach?>

	<?
	/*
	Выводим пагинацию. 4 параметр ведет на шаблон компонента пагинации, но не на template.php,
	а на sections.php. Этот момент будет объяснен дальше
	*/
	?>
	<?if($arParams['DISPLAY_BOTTOM_PAGER'] == 'Y'):?>
		<?$rsNav->NavPrint("Разделы", true, "text", SITE_TEMPLATE_PATH.'/components/bitrix/system.pagenavigation/ajax/sections.php');?>
	<?endif?>
<?endif?>
```

## Подготовка переменных для компонента пагинации

В компоненте *catalog.section.list* был указан указан путь к шаблону, но не на *template.php*, а на *sections.php*:

`SITE_TEMPLATE_PATH.'/components/bitrix/system.pagenavigation/ajax/sections.php`

Дело в том, что шаблон пагинации работает на основе подготовленного массива `$arResult`. Она формируется в самом компоненте пагинации (*system.pagenavigation*). Но если мы вызываем шаблон через метод `CDBResult::NavPrint`, этот массив не создается. По-этому, используем отдельный шаблон-префикс. В нем собираем массив `$arResult` из перменных объекта резульата `CDBResult` и подключаем основной файл шаблона, находящийся в той же папке.

```php title="sections.php"
<?
$arResult["NavShowAlways"] = $this->bFirstPrintNav;
$arResult["NavPageNomer"] = $this->NavPageNomer;
$arResult["NavPageCount"] = $this->NavPageCount;
$arResult["NavPageSize"] = $this->NavPageSize;
$arResult["NavRecordCount"] = $this->NavRecordCount;
$arResult["bShowAll"] = $this->bShowAll;
$arResult["NavShowAll"] = $this->NavShowAll;
$arResult["NavNum"] = $this->NavNum;
$arResult["bDescPageNumbering"] = $this->bDescPageNumbering;
$arResult["nStartPage"] = $this->nStartPage;
$arResult["nEndPage"] = $this->nEndPage;

$arResult["NavQueryString"] = $strNavQueryString;
$arResult["sUrlPath"] = $sUrlPath;

include __DIR__.'/template.php';
```

:::info Примечание
Подключение шаблона пагинации происходит в методе `CDBResult::GetNavPrint`. По-этому в `sections.php` доступны так же переменные **$strNavQueryString** и **$sUrlPath**, формируемые внутри этого метода.
:::