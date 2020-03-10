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

### Соединение страницы index.php с lang-файлом, лежащим в другой директории
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

### Links for instagram components
```php

"https://www.instagram.com/{'INSTAGRAM_USER_NAME'}/?__a=1";
"https://instagram.com/graphql/query/?query_id=17888483320059182&id={'USER_ID'}&first={'IMAGES_COUNT'}";

```

