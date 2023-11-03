---
sidebar_position: 2
---

# Ajax запросы и контроллеры

Вместо того чтобы создавать отдельный файл-обработчик на ajax запрос, можно делать запросы к методам класса (действиям контролллера) класса модуля. Это реализуется с помощью функционала [контроллеров](https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=43&LESSON_ID=6436&LESSON_PATH=3913.3516.5062.3750.6436) Битрикса.

```php title="Контроллер /local/modules/ewp.mymodule/lib/controller/region.php"
class Region extends \Bitrix\Main\Engine\Controller
{
	public function configureActions()
	{
		return [
			'getRegion' => [
				'prefilters' => [],
				'postfilters' => []
			],
		];
	}

	public function getRegionAction()
	{
		return 'Москва';
	}
}
```

```js title="js ajax запрос"
BX.ajax.post(
	'/bitrix/services/main/ajax.php?action=ewp:mymodule.api.Region.getRegion',
	{
	},
	function(response)
	{
		response = JSON.parse(response);
		if (!response) return;
		
		//Москва
		$result = response.data;
	}
);
```

## Как начать?

### Создание контроллера

Для начала нужно создать контроллер. Создаем php файл по пути `/local/modules/ewp.mymodule/lib/controller/`, например `region.php`.

Добавим в него 2 действия:
- `getRegion` - получить регион (всегда возвращает строку "Москва")
- `setRegion` - установить регион (заглушка, ничего не делает, возвращает "Регион установлен")

```php title="/local/modules/ewp.mymodule/lib/controller/region.php"
namespace Ewp\MyModule\Controller;

//Класс контроллера должен наследоваться от \Bitrix\Main\Engine\Controller
class Region extends \Bitrix\Main\Engine\Controller
{
	//Конфигуратор действий
	public function configureActions()
	{
		return [
			'getRegion' => [
				'prefilters' => [],
				'postfilters' => []
			],
			'setRegion' => [
				'prefilters' => [],
				'postfilters' => []
			],
		];
	}

	//Действие getRegion. В конце метода обязательно должен быть постфикс Action
	public function getRegionAction()
	{
		return 'Москва';
	}

	//Действие setRegion
	public function setRegionAction($city)
	{
		return 'Регион установлен';
	}
}
```

### Добавление контроллерав в автозагрузку

Чтобы к контроллеру можно было обращаться, его нужно добавить в автозагрузку

```php title="/local/modules/ewp.mymodule/include.php"
use \Bitrix\Main\Loader;

Loader::registerAutoLoadClasses(
	'ewp.mymodule',
	[
		'\\Ewp\\MyModule\\Controller\\Region' => 'lib/controller/region.php',
	]
);
```

### Добавление пространства имени для контроллера

Теперь нужно пространству имени `Ewp\MyModule\Controller` задать короткое название для ajax запросов, например `api`

```php title="/local/modules/ewp.mymodule/.settings.php"
return [
	'controllers' => [
		'value' => [
			'namespaces' => [
				'\\Ewp\\MyModule\\Controller' => 'api',
			]
		],
		'readonly' => true
	]
];
```

### Ajax запросы

Все готово. Теперь можно обращаться к методам класса черех ajax по url `/bitrix/services/main/ajax.php?action=vendor:modulename.namespace.Controller.Action`, где
- vendor - разработчик модуля
- modulename - название модуля
- namespace - пространство имени
- Controller - контролллер
- Action - действие контролллера

```js title="Ajax запрос на получение региона"
BX.ajax.post(
	'/bitrix/services/main/ajax.php?action=ewp:mymodule.api.Region.getRegion',
	{
	},
	function(response)
	{
		response = JSON.parse(response);
		if (!response) return;
		
		//Москва
		$result = response.data;
	}
);
```

```js title="Ajax запрос на установку региона"
BX.ajax.post(
	'/bitrix/services/main/ajax.php?action=ewp:mymodule.api.Region.setRegion',
	{
		regin: 'Пенза'
	},
	function(response)
	{
		response = JSON.parse(response);
		if (!response) return;
		
		//Регион установлен
		$result = response.data;
	}
);
```