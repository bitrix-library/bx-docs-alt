### Вывод спреда в строку с отсеиванием пустых элементов
```php

/**
 * @param string $delimiter
 * @param mixed ...$parts
 * @return string|null
 */
function combine(string $delimiter, ...$parts): ?string
{
	$result = '';
	for ($i = 0; $i < count($parts); $i++) {
		if (strlen($parts[$i]) === 0) {
			unset($parts[$i]);
		}
	}
	for ($i = 0; $i < count($parts); $i++) {
		$result .= $parts[$i];
		if ($i !== count($parts) - 1) {
			$result .= $delimiter;
		}
	}
	return $result;
}

```

### Отсеивание спреда с пустыми элементами
```php

/**
 * @param mixed ...$parts
 * @return string|null
 */
function allOrNothing(...$parts): ?string
{
	$result = '';
	foreach ($parts as $part) {
		if (strlen($part) === 0) {
			return false;
		}
		$result .= $part;
	}
	return $result;
}

```

### Использование lang-файла, лежащего в другой директории на главной,
```php

use Bitrix\Main\Localization\Loc;

Loc::loadLanguageFile($_SERVER["DOCUMENT_ROOT"] . SITE_TEMPLATE_PATH . "/index.php");

Loc::getMessage("EXAMPLE");

```

### Сортировка элементов из catalog.section.list
```php

function tree(array &$haystack, int $current): void
{
	for ($i = $current + 1; array_key_exists($i, $haystack); $i++) {
		if ($haystack[$i]["DEPTH_LEVEL"] > $haystack[$current]["DEPTH_LEVEL"]) {
			$haystack[$current]["CHILDS"][] = $haystack[$i];

			array_splice($haystack, $i, 1);
			$i--;
		}
	}

	if (array_key_exists($current - 1, $haystack)) {
		tree($haystack, $current - 1);
	}
}

tree($arResult["SECTIONS"], count($arResult["SECTIONS"]));


```

### Разбиение массива на части
```php

$arResult = (function (array $list, $p) {
	$listlen = count($list);
	$partlen = floor($listlen / $p);
	$partrem = $listlen % $p;
	$partition = array();
	$mark = 0;
	for($px = 0; $px < $p; $px ++) {
		$incr = ($px < $partrem) ? $partlen + 1 : $partlen;
		$partition[$px] = array_slice($list, $mark, $incr);
		$mark += $incr;
	}
	return $partition;
})($arResult, 3);

```

### Полезные ссылки для компонентов инстаграма
```php

"https://www.instagram.com/{'INSTAGRAM_USER_NAME'}/?__a=1";
"https://instagram.com/graphql/query/?query_id=17888483320059182&id={'USER_ID'}&first={'IMAGES_COUNT'}";

```

### Resize image с сохранением оригинала
```php

foreach($arResult["ITEMS"] as &$arItem) {
	$arItem["PREVIEW_PICTURE"] = array(
		"PREVIEW" => CFile::ResizeImageGet($arItem["PREVIEW_PICTURE"]["ID"], array("width" => 1800, "height" => 1000), false, false, false, false, 75),
		"ORIGINAL" => $arItem["PREVIEW_PICTURE"]
	);
}

```

### Получение информации о секции/инфоблоке
```php

$db_list = CIBlockSection::GetByID($arParams["PARENT_SECTION"]);
if ($db_el = $db_list->GetNext()) {
	$arResult["SECTION"] = $db_el;
}

$db_list = CIBlock::GetByID($arParams["IBLOCK_ID"]);
if ($db_el = $db_list->GetNext()) {
	$arResult["IBLOCK"] = $db_el;
}
```

### Получение секций всех элементов без повторений
```php

foreach ($arResult["ITEMS"] as &$arItem) {
	if (empty($arResult["SECTIONS"][$arItem["IBLOCK_SECTION_ID"]])) {
		$db_list = CIBlockSection::GetByID($arItem["IBLOCK_SECTION_ID"]);
		if ($db_el = $db_list->GetNext()) {
			$arResult["SECTIONS"][$arItem["IBLOCK_SECTION_ID"]] = array("NAME" => $db_el["NAME"], "SORT" => $db_el["SORT"], "ID" => $arItem["IBLOCK_SECTION_ID"]);
		}
		unset($db_list);
	}
}

```

### Удаление дублей в css файле с помощью node.js
```
// Глобальная установка css-purge 
npm install css-purge -g
// Удаление дублей в файле style.css
css-purge -i style.css -o style_purged.css
```
