# Icon Selector (ot-iconselector)

When `ot_iconselector` is installed, `icon_identifier` fields should use the visual icon picker.

**Top-level fields** (`useExistingField: true`): These inherit the full TCA config from `tt_content.icon_identifier`, including any `renderType` set by a TCA override. Ensure the sitepackage has a TCA override on `tt_content` that applies `renderType: otIconSelector`. Only set `renderType` — never change `type` (TYPO3 derives DB schema from `type`).

```php
// Configuration/TCA/Overrides/tt_content.php
if (ExtensionManagementUtility::isLoaded('ot_iconselector')) {
    $GLOBALS['TCA']['tt_content']['columns']['icon_identifier']['config']['renderType'] = 'otIconSelector';
}
```

**Collection child fields**: `useExistingField: true` is not available inside Collections (separate child table). Create a TCA override file named after the child table (naming pattern: `cb_{blockname}_{field}`):

```php
// Configuration/TCA/Overrides/cb_{blockname}_{field}.php
if (ExtensionManagementUtility::isLoaded('ot_iconselector')) {
    $GLOBALS['TCA']['cb_{blockname}_{field}']['columns']['icon_identifier']['config']['renderType'] = 'otIconSelector';
}
```
