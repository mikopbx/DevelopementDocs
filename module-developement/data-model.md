---
description: >-
  Phalcon 5 models, table conventions, relationships and metadata in a MikoPBX
  module.
---

# Data model

Start every module from a clean data structure. A module owns its own SQLite database, and each table is described by a Phalcon 5 ORM model class. The model is the single source of truth for the schema: MikoPBX reads the `@Column` annotations and **auto-creates** the matching table during installation. You never write `CREATE TABLE` SQL by hand.

This page covers how to declare a model, the column conventions MikoPBX expects, how tables are generated, and how to wire relationships — including dynamic relations to core models.

For the surrounding directory layout and class roles, see [module-anatomy.md](module-anatomy.md). For how tables get created on install, see [module-installer.md](module-installer.md). For the core models you will relate to, see [core.md](../core.md).

{% hint style="info" %}
**Reference:** Phalcon 5.x ORM models — [https://docs.phalcon.io/5.0/en/db-models](https://docs.phalcon.io/5.0/en/db-models). MikoPBX 2025.1.1+ ships Phalcon 5.9.3 on PHP 8.4; annotation syntax (`@Column`, `@Primary`, `@Identity`) is unchanged from earlier Phalcon, but the runtime APIs are Phalcon 5.
{% endhint %}

## Where models live

Model classes live in the module's `Models/` directory. The class name and file name must match (PSR-4), and the namespace is `Modules\{ModuleUniqueID}\Models`.

```
ModuleBlackList
├── Models
│   ├── BlackListNumbers.php
│   └── BlackListSettings.php
├── Lib
│   └── BlackListConf.php
├── module.json
└── ...
```

Our running example module, **ModuleBlackList**, stores blocked caller numbers in a `BlackListNumbers` model backed by the table `m_BlackListNumbers`.

{% hint style="success" %}
See the working model in `Extensions/ModuleTemplate/Models/ModuleTemplate.php` and a v3-style model in `Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv3/Models/Tasks.php`.
{% endhint %}

## The base class

A module model extends `MikoPBX\Modules\Models\ModulesModelsBase`:

```php
use MikoPBX\Modules\Models\ModulesModelsBase;

class BlackListNumbers extends ModulesModelsBase { /* ... */ }
```

`ModulesModelsBase` (in `Core/src/Modules/Models/ModulesModelsBase.php`) does one critical thing in its `initialize()`: it derives the module's unique ID from the namespace (`Modules\{UniqueID}\Models\...`) and points the model at the **module's own database connection** via `setConnectionService()`. This is why a module model reads/writes the module DB rather than the main PBX DB. It then calls `parent::initialize()`, which is `ModelsBase::initialize()` — enabling snapshot tracking (`keepSnapshots(true)`), change events, and dynamic relations.

{% hint style="warning" %}
Always extend `ModulesModelsBase`, not `Phalcon\Mvc\Model` directly. If you extend `MikoPBX\Common\Models\ModelsBase` instead, the model is bound to the **main** PBX database connection, not your module's database. `Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv3/Models/Tasks.php` extends `ModelsBase` directly — that is a deliberate REST-API example choice; for ordinary module tables in your own DB, extend `ModulesModelsBase`.
{% endhint %}

## Declaring a model

The model body is the table schema. Each public property annotated with `@Column` becomes a column; the annotation defines the SQL type.

{% code title="Models/BlackListNumbers.php" %}
```php
<?php

declare(strict_types=1);

namespace Modules\ModuleBlackList\Models;

use MikoPBX\Modules\Models\ModulesModelsBase;

class BlackListNumbers extends ModulesModelsBase
{
    /**
     * @Primary
     * @Identity
     * @Column(type="integer", nullable=false)
     */
    public $id;

    /**
     * The blocked phone number.
     *
     * @Column(type="string", nullable=true)
     */
    public ?string $number = '';

    /**
     * Optional note shown in the UI.
     *
     * @Column(type="string", nullable=true)
     */
    public ?string $description = '';

    /**
     * Boolean stored as a 1-char string ('0' / '1') in SQLite.
     *
     * @Column(type="string", length=1, nullable=true, default="0")
     */
    public ?string $enabled = '0';

    /**
     * Nullable foreign key to a core Providers row (no link if null).
     *
     * @Column(type="integer", nullable=true)
     */
    public ?int $providerId = null;

    public function initialize(): void
    {
        $this->setSource('m_BlackListNumbers');
        parent::initialize();
    }
}
```
{% endcode %}

### `setSource()` — the table name

`initialize()` must call `$this->setSource('m_BlackListNumbers')` to bind the model to its table, then call `parent::initialize()`. The base class does the connection wiring, so **never skip `parent::initialize()`** — without it the model has no DB connection and no event handling.

This mirrors the core models exactly. `Sip` calls `$this->setSource('m_Sip')`, `Extensions` calls `$this->setSource('m_Extensions')` (see `Core/src/Common/Models/Sip.php` and `Core/src/Common/Models/Extensions.php`).

## Column conventions

MikoPBX/Phalcon models follow a strict set of conventions. They look unusual compared to plain PHP DTOs, but they are correct and intentional — the linter explicitly exempts them from the "missing type declaration" rule (see anti-pattern #15 in `Core/.claude/skills/mikopbx-module/reference/anti-patterns.md`).

### Primary key: always untyped `public $id`

```php
/**
 * @Primary
 * @Identity
 * @Column(type="integer", nullable=false)
 */
public $id;
```

The primary key is declared **without** a PHP type hint. This matches every core model — `Sip`, `Extensions`, `Providers` all use untyped `public $id;` (confirmed in `Core/src/Common/Models/Sip.php` and `Core/src/Common/Models/Extensions.php`). The `@Identity` annotation makes it an auto-increment column. Do not write `public int $id;` for the primary key — Phalcon assigns it from the database, and an uninitialized typed property throws a "must not be accessed before initialization" error.

### String columns: nullable, with a default

```php
/**
 * @Column(type="string", nullable=true)
 */
public ?string $number = '';
```

Use a nullable type (`?string`) and an empty-string default. This keeps the property readable before the first `save()` and matches core models such as `Sip::$uniqid` (`public ?string $uniqid = '';`).

### Integers stored as string in SQLite

SQLite has dynamic typing, and MikoPBX stores small flags as 1-character strings. A boolean flag is declared as a **string** column of length 1 with a string default `'0'` / `'1'`:

```php
/**
 * @Column(type="string", length=1, nullable=true, default="0")
 */
public ?string $enabled = '0';
```

This is the exact pattern used by core models — `Extensions` and `Sip` declare flags as `@Column(type="string", length=1, nullable=true, default="0")`. Read them as `'0'`/`'1'` strings, not PHP booleans. When you need a real integer column (counters, priorities), use `type="integer"`:

```php
/**
 * @Column(type="integer", default="1", nullable=true)
 */
public ?int $priority = 1;
```

`Extensions/ModuleTemplate/Models/ModuleTemplate.php` shows both styles: text and dropdown fields as `type="string"`, and checkbox/toggle fields as `type="integer", default="1"`.

### Nullable integer foreign keys

A column that references another table's `id` is a nullable integer that is `null` when there is no link:

```php
/**
 * @Column(type="integer", nullable=true)
 */
public ?int $providerId = null;
```

### Defaults are filled automatically

`ModelsBase::beforeValidationOnCreate()` reads the annotation `default` values from metadata and applies them to any property still unset on insert (see `Core/src/Common/Models/ModelsBase.php`). So the `default="0"` in the annotation — not just the PHP property default — is what backs a new row.

{% hint style="warning" %}
**Anti-pattern #7 — phantom fields.** Only properties declared with a `@Column` annotation are real columns. Reading or writing any other property name on a model instance silently returns `null` (or is dropped on save) and causes subtle logic bugs. If you need a new field, add a `@Column` property and re-install the module so the table is regenerated. Never reference a field that is not in the model. See `Core/.claude/skills/mikopbx-module/reference/anti-patterns.md` (#7).
{% endhint %}

## How tables are created

You do not run migrations manually. During installation, `PbxExtensionSetupBase::createSettingsTableByModelsAnnotations()` scans the module's `Models/*.php` files and builds or upgrades each table from its annotations:

{% code title="Core/src/Modules/Setup/PbxExtensionSetupBase.php" %}
```php
public function createSettingsTableByModelsAnnotations(): bool
{
    ModulesDBConnectionsProvider::recreateModulesDBConnections();
    $results = glob($this->moduleDir . '/Models/*.php', GLOB_NOSORT);
    $dbUpgrade = new UpdateDatabase();
    foreach ($results as $file) {
        $className        = pathinfo($file)['filename'];
        $moduleModelClass = "Modules\\{$this->moduleUniqueID}\\Models\\$className";
        $upgradeResult = $dbUpgrade->createUpdateDbTableByAnnotations($moduleModelClass);
        if (!$upgradeResult) {
            return false;
        }
    }
    ModulesDBConnectionsProvider::recreateModulesDBConnections();
    return true;
}
```
{% endcode %}

Each model is handed to `UpdateDatabase::createUpdateDbTableByAnnotations()`, which creates the table on first install and **adds new columns** on upgrade when you add new `@Column` properties. This is why the rule for adding a field is "edit the model, then re-install" — see [module-installer.md](module-installer.md) for the full install flow.

The module database (SQLite) is created at:

```bash
/storage/usbdisk1/mikopbx/custom_modules/ModuleBlackList/db/module.db
```

Verify a table was created:

```bash
sqlite3 /storage/usbdisk1/mikopbx/custom_modules/ModuleBlackList/db/module.db .tables
```

### Naming requirements

* The table name passed to `setSource()` must be unique within the module.
* The model class name must be unique within the module.
* Prefix module tables with `m_` so they never collide with core tables.

## Relationships

Declare relationships inside `initialize()` (after `setSource()`, before `parent::initialize()`), using the standard Phalcon methods `belongsTo`, `hasOne`, `hasMany`, `hasManyToMany`.

`ModuleTemplate` shows a `hasOne` link from a module column to the core `Providers` model:

{% code title="Extensions/ModuleTemplate/Models/ModuleTemplate.php" %}
```php
use MikoPBX\Common\Models\Providers;
use Phalcon\Mvc\Model\Relation;

public function initialize(): void
{
    $this->setSource('m_ModuleTemplate');
    $this->hasOne(
        'dropdown_field',          // local column
        Providers::class,          // referenced model
        'id',                      // referenced column
        [
            'alias'      => 'Providers',
            'foreignKey' => [
                'allowNulls' => true,
                'action'     => Relation::NO_ACTION,
            ],
        ]
    );
    parent::initialize();
}
```
{% endcode %}

The `foreignKey.action` controls delete behaviour and is enforced by `ModelsBase::beforeDelete()` (it walks the relations and blocks a delete that would orphan related rows). Use `Relation::ACTION_RESTRICT` to block, `Relation::ACTION_CASCADE` to cascade, or `Relation::NO_ACTION` to skip enforcement. The core architecture (Users → Extensions → Sip, Providers → routing tables) is summarised in [core.md](../core.md).

### Dynamic relations to core models — `getDynamicRelations()`

The relation above lets **your** model reach a core model. To make a **core** model aware of your module (e.g. so deleting a `Providers` row checks your table first, or so the admin UI shows the dependency), implement the static hook `getDynamicRelations()`.

MikoPBX calls it automatically. In `ModelsBase::initialize()`, `addExtensionModulesRelations()` iterates every enabled module's `Models/*.php`, and for each class that defines `getDynamicRelations()` it invokes the method, passing the core model instance being initialised (see `Core/src/Common/Models/ModelsBase.php`). Your hook inspects which model is calling and, if it is the one you want to extend, adds the relation onto it.

{% code title="Models/BlackListNumbers.php" %}
```php
use MikoPBX\Common\Models\Providers;
use Phalcon\Mvc\Model\Relation;

/**
 * Registers a relation from the core Providers model back to this module.
 * MikoPBX calls this for every core model during its initialize().
 */
public static function getDynamicRelations(&$calledModelObject): void
{
    if (is_a($calledModelObject, Providers::class)) {
        $calledModelObject->belongsTo(
            'id',
            BlackListNumbers::class,
            'providerId',
            [
                'alias'      => 'ModuleBlackListNumbers',
                'foreignKey' => [
                    'allowNulls' => 0,
                    // The message alias MUST duplicate the relation alias after "Models\"
                    'message'    => 'Models\ModuleBlackListNumbers',
                    'action'     => Relation::ACTION_RESTRICT,
                ],
            ]
        );
    }
}
```
{% endcode %}

{% hint style="info" %}
The commented-out template in `Extensions/ModuleTemplate/Models/ModuleTemplate.php` (the `getDynamicRelations()` body) is the canonical reference for this hook, including the rule that the `message` value must duplicate the `alias` prefixed with `Models\`.
{% endhint %}

{% hint style="warning" %}
Dynamic relations are only registered for modules whose `module.json` declares `min_pbx_version` at or above `2020.2.468` (the `ModelsBase::MIN_MODULE_MODEL_VER` constant). For MikoPBX 2025.1.1+ modules this is always satisfied — just make sure `min_pbx_version` is set correctly in `module.json`.
{% endhint %}

## Public IDs: the v3 convention

Several core models expose an internal auto-increment `id` plus a separate public string identifier — for example `Sip::$uniqid` (`public ?string $uniqid = '';`). The same pattern is recommended for module tables exposed over the REST API, so you never leak sequential IDs (which enable enumeration).

The v3 table convention is `m_{Module}_{Table}` with a `uniqid` public column. `Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv3/Models/Tasks.php` demonstrates it:

{% code title="Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv3/Models/Tasks.php" %}
```php
use MikoPBX\Common\Models\ModelsBase;

class Tasks extends ModelsBase
{
    /**
     * @Primary
     * @Identity
     * @Column(type="integer", nullable=false)
     */
    public int $id;

    /**
     * Public-facing unique identifier — expose this in the API, not $id.
     *
     * @Column(type="string", nullable=false)
     */
    public string $uniqid;

    public function initialize(): void
    {
        $this->setSource('m_ModuleExampleRestAPIv3_Tasks');
        parent::initialize();
    }
}
```
{% endcode %}

{% hint style="warning" %}
The `Tasks` example types its primary key as `public int $id;` and its public id as `public string $uniqid;`. That works in that REST-API sample, but the **canonical** MikoPBX convention — and what the linter expects — is an untyped `public $id;` and a nullable `public ?string $uniqid = '';` (as in `Sip`). Prefer the core-model style for your own tables.

There is no automatic `uniqid` generator hook on `ModelsBase`. The base class provides the static helper `ModelsBase::generateUniqueID(string $alias = ''): string` (which returns `"{$alias}-{HASH}"`, where `{HASH}` is 4 random bytes rendered as 8 uppercase hex characters), but you must call it yourself — for example in a `beforeValidationOnCreate()` override or just before `save()`. Set `uniqid` explicitly; do not assume it is populated for you.
{% endhint %}

## Using the model

Once installed, use standard Phalcon ORM calls. Always parameterise conditions (string interpolation in `find`/`findFirst` is a SQL-injection vector — anti-pattern S2) and always check the result of `save()`.

```php
use Modules\ModuleBlackList\Models\BlackListNumbers;

// Safe, parameterised query
$blocked = BlackListNumbers::find([
    'conditions' => 'enabled = :on: AND number = :num:',
    'bind'       => ['on' => '1', 'num' => $callerId],
]);

// Create / update with validation
$row = new BlackListNumbers();
$row->number     = $callerId;
$row->enabled    = '1';
$row->providerId = null;
if (!$row->save()) {
    $errors = $row->getMessages(); // never ignore save() failures
}
```

## Checklist

* [ ] Model extends `ModulesModelsBase` (module DB) — or `ModelsBase` only for deliberate main-DB / REST-API cases.
* [ ] `initialize()` calls `setSource('m_...')` then `parent::initialize()`.
* [ ] Primary key is untyped `public $id;` with `@Primary @Identity @Column(type="integer", nullable=false)`.
* [ ] String columns are `?string` with an empty-string default; boolean flags are `type="string", length=1, default="0"`.
* [ ] Foreign keys are `?int`, nullable.
* [ ] Every accessed property has a `@Column` annotation (no phantom fields).
* [ ] Relations to core models declared via `hasOne`/`belongsTo`; reverse relations onto core models via `getDynamicRelations()`.
* [ ] Re-install the module after any schema change so `createSettingsTableByModelsAnnotations()` rebuilds the table.
