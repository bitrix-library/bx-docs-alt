### Вывод спреда в строку с отсеиванием пустых элементов
```php

/**
 * @param string $delimiter
 * @param mixed ...$parts
 * @return string|null
 */
function combine(string $delimiter, ...$parts): ?string
{
	$result = "";
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
	$result = "";
	foreach ($parts as $part) {
		if (strlen($part) === 0) {
			return false;
		}
		$result .= $part;
	}
	return $result;
}

```

### Использование lang-файла, лежащего в другой директории
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

### Полезные ссылки для получения информации из инстаграма
```php

"https://www.instagram.com/{'INSTAGRAM_USER_NAME'}/?__a=1";
"https://instagram.com/graphql/query/?query_id=17888483320059182&id={'USER_ID'}&first={'IMAGES_COUNT'}";

```

### Resize image
```php

$picture = CFile::ResizeImageGet($picture, array("width" => 1800, "height" => 1000), false, false, false, false, 75);

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

### Получение всех элементов highload блока
```php
if (CModule::IncludeModule("highloadblock")) {
    $arHLBlock = Bitrix\Highloadblock\HighloadBlockTable::getById(HL_BLOCK_ID)->fetch();
    $obEntity = Bitrix\Highloadblock\HighloadBlockTable::compileEntity($arHLBlock);
    $strEntityDataClass = $obEntity->getDataClass();

    $db_list = $strEntityDataClass::getList(array(
        "filter" => array("UF_XML_ID" => ARRFILTR),
        "select" => array("*"),
	"order" => array("ID" => "ASC")
    ));

    while ($db_el = $db_list->Fetch()) {
        dump($db_el);
    }
}
```

### Удаление всех секций инфоблока
```php
if (CModule::IncludeModule("iblock")) {
    $db_list = CIBlockSection::GetList(
        array("ID" => "ASC"),
        array("IBLOCK_ID" => IBLOCK_ID)
    );
    while ($db_el = $result->db_list()) {
        CIBlockSection::Delete($db_el["ID"]);
    }
}
```

### Удаление всех элементов инфоблока
```php
if (CModule::IncludeModule("iblock")) {
    $db_list = CIBlockElement::GetList(
        array("ID" => "ASC"),
        array("IBLOCK_ID" => IBLOCK_ID)
    );
    while ($db_el = $result->db_list()) {
        CIBlockElement::Delete($db_el["ID"]);
    }
}
```

### Обновление сео свойств элемента инфоблока
```php
$ipropTemplates = new \Bitrix\Iblock\InheritedProperty\ElementTemplates(IBLOCK_ID, ELEMENT_ID);
$ipropTemplates->set(array(
    "ELEMENT_META_TITLE" => "Example",
    "ELEMENT_META_KEYWORDS" => "Example",
    "ELEMENT_META_DESCRIPTION" => "Example",
));
```

### Удаление дублей в css файле
```
// Удаление
npm install css-purge -g	
css-purge -i style.css -o style_purged.css	
// Оптимизация
https://css.github.io/csso/csso.html
```

### Пример ссылок для bitrix:main.share
```
https://yandex.ru/dev/share/
```

### Событие для изменения Коэффициента единицы измерения товара при обновлении элемента инфоблока
```php
$event_manager = EventManager::getInstance();
$event_manager->AddEventHandler("iblock", "OnAfterIBlockElementUpdate", "handlerOnAfterIBlockElementUpdate");

function handlerOnAfterIBlockElementUpdate($arFields): void
{
    if (($arFields["RESULT"] === true) && $arFields["IBLOCK_ID"] === CATALOG_IBLOCK_ID) {

        $event_manager = EventManager::getInstance();

        $event_manager->AddEventHandler("main", "OnAfterEpilog", function () use ($arFields) {
            $db_elements = CIBlockElement::GetList(
                array('SORT' => 'ASC'),
                array(
                    'ID' => $arFields["ID"],
                    'IBLOCK_ID' => CATALOG_IBLOCK_ID,
                    '!PROPERTY_KOLICHESTKO_V_UPAKOVKE_M2' => false,
                ),
                false,
                false,
                array(
                    'ID',
                    'IBLOCK_ID',
                    'PROPERTY_KOLICHESTKO_V_UPAKOVKE_M2',
                )
            );

            if ($db_el = $db_elements->GetNext()) {
                $db_ratio = CCatalogMeasureRatio::getList(
                    array('SORT' => 'ASC'),
                    array(
                        'IBLOCK_ID' => $db_el['IBLOCK_ID'],
                        'PRODUCT_ID' => $db_el['ID'],
                        'IS_DEFAULT' => 'Y'
                    ),
                    false,
                    false,
                    array(
                        'ID',
                        'PRODUCT_ID',
                        'RATIO',
                    )
                );
                if ($db_ar_ratio = $db_ratio->GetNext()) {
                    $ratio_id = $db_ar_ratio['ID'];
                }
                if (!empty($ratio_id)) {
                    CCatalogMeasureRatio::update(
                        $ratio_id,
                        array(
                            'PRODUCT_ID' => $db_el['ID'],
                            'RATIO' => $db_el['PROPERTY_KOLICHESTKO_V_UPAKOVKE_M2_VALUE'],
                            'IS_DEFAULT' => 'Y'
                        ));
                } else {
                    CCatalogMeasureRatio::add(
                        array(
                            'PRODUCT_ID' => $db_el['ID'],
                            'RATIO' => $db_el['PROPERTY_KOLICHESTKO_V_UPAKOVKE_M2_VALUE'],
                        )
                    );
                }
            }
        });
    }
}
```
