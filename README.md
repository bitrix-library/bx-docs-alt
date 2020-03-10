### Составление конструкций из переменных с учетом возможности их отсутствия
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

### Соединение с lang-файлом Bitrix, лежащим в другой директории
```php

use Bitrix\Main\Localization\Loc;

Loc::loadLanguageFile($_SERVER["DOCUMENT_ROOT"] . SITE_TEMPLATE_PATH . "/index.php");

Loc::getMessage("EXAMPLE");

```

### Сортировка элементов из catalog.section.list Bitrix
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

### Разбиение массива на 3 поочередно
```php

$arResult = (function (Array $list, $p) {
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

### Instagram links for templates
```php

$this->arResult["SOURCE"] = "https://www.instagram.com/{$this->arParams['INSTAGRAM_USER_NAME']}/?__a=1";
$this->arResult["SOURCE"] = "https://instagram.com/graphql/query/?query_id=17888483320059182&id={$this->arParams['INSTAGRAM_USER_ID']}&first={$this->arParams['INSTAGRAM_IMAGES_COUNT']}";

```
```js

<script type="application/javascript">
    `use strict`;

    document.addEventListener(`DOMContentLoaded`, event => {
        ((button, panel, list) => {
            if (BX.getCookie(`instagram-panel`) == `open`) {
                button.classList.remove(`active`);
                panel.style.display = null;
            } else if (BX.getCookie(`instagram-panel`) == `close`) {
                button.classList.add(`active`);
                panel.style.display = `none`;
            } else {
                button.classList.remove(`active`);
                panel.style.display = null;
            }

            $.ajax({
                method: `get`,
                url: `<?= $arResult["SOURCE"] ?>`,
                success: (data) => {
                    try {
                        for (let el of data.data.user.edge_owner_to_timeline_media.edges) {
                            list.insertAdjacentHTML(
                                `beforeend`,
                                `<li>
                                    <a href="<?=$arParams["INSTAGRAM_URL"]?>" target="_blank">
                                        <img src="${el.node.thumbnail_resources[<?=$arParams["INSTAGRAM_THUMBNAIL_RESOURCES_TYPE"]?>].src}">
                                    </a>
                                </li>`
                            )
                        }
                    } catch (e) {
                        console.log(`Instagram component failed: ${e.message}`)
                    }
                }
            });
        })(document.getElementById(`template`), document.querySelector(`.element`), document.querySelector(`.list`));
    });
</script>

```
