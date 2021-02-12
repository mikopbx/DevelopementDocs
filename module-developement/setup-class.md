---
description: 'This class helps to install, setup and delete an extension module'
---

# Setup class

On the template repository, you can find an example in the file ModuleTemplate/Setup/**PbxExtensionSetup.php**

![MikoPBX setup class](../.gitbook/assets/nbscreenshot-2021-02-12-at-13.23.49.png)

  
****Your [PbxExtensionSetup](https://github.com/mikopbx/ModuleTemplate/blob/master/Setup/PbxExtensionSetup.php) class should inherit [PbxExtensionSetupBase](https://github.com/mikopbx/Core/blob/master/src/Modules/Setup/PbxExtensionSetupBase.php) and can override any public functions.

## Install module procedure

There are two ways to install a MikoPBX extension:

* over a ZIP archive local file
* from the MIKO modules repository

For a development purpose,  we recommend using just ZIP archive.

When you upload any new module the MikoPBX system unzips all files and calls  the next procedure:

```php
$pbxExtensionSetupClass = "\\Modules\\{$moduleUniqueID}\\Setup\\PbxExtensionSetup";
$setup = new $pbxExtensionSetupClass($moduleUniqueID);
$setup->installModule();        
```

After initializing the **PbxExtensionSetup** class you will have a lot of useful, within installation process, variables:

```php
/**
 * Trial product version identify number from module.json
 *
 * @var int
 */
public $lic_product_id;

/**
 * License feature identify number from module.json
 *
 * @var int
 */
public $lic_feature_id;

/**
 * Module unique identify  from module.json
 *
 * @var string
 */
protected string $moduleUniqueID;

/**
 * Module version from module.json
 *
 * @var string
 */
protected $version;

/**
 * Minimal require version PBX
 *
 * @var string
 */
protected $min_pbx_version;

/**
 * Module developer name
 *
 * @var string
 */
protected $developer;

/**
 * Module developer's email from module.json
 *
 * @var string
 */
protected $support_email;

/**
 * PBX general database
 *
 * @var \Phalcon\Db\Adapter\Pdo\Sqlite
 */
protected $db;

/**
 * Folder with module files
 *
 * @var string
 */
protected string $moduleDir;

/**
 * Phalcon config service
 *
 * @var \Phalcon\Config
 */
protected $config;

/**
 * License worker
 *
 * @var \MikoPBX\Service\License
 */
protected $license;

/**
 * Error and verbose messages
 *
 * @var array
 */
protected array $messages;
```

Usually, you shouldn't override the **PbxExtensionSetup** class constructor, but if you want don't forget to left parent class initialization.   
For example, you want to add some extra variable **ModuleExtension** to your setup class:

```php
class PbxExtensionSetup extends PbxExtensionSetupBase
{

    private string $ModuleExtension;

    /**
    * PbxExtensionSetup constructor.
    *
    * @param string $moduleUniqueID - the unique module identifier
    */
    public function __construct(string $moduleUniqueID)
    {
        parent::__construct($moduleUniqueID);
        //... some extra code ... //
        $this->ModuleExtension = '000XXXX';
    }
//... some extra code ... //
}
```

As you can see the main setup function is **InstallModule**. It is already written on **PbxExtensionSetupBase.** The main module installation function called by PBXCoreRest interface after unzipping module files. It calls some private functions and sets error messages on the **message** variable. If something going wrong it method will return **false** and the user will be announced with information from the **message** variable**.**

```php
public function installModule(): bool
{
    $result = true;
    try {
        if ( ! $this->activateLicense()) {
            $this->messages[] = 'License activate error';
            $result           = false;
        }
        if ( ! $this->installFiles()) {
            $this->messages[] = ' installFiles error';
            $result           = false;
        }
        if ( ! $this->installDB()) {
            $this->messages[] = ' installDB error';
            $result           = false;
        }
        if ( ! $this->fixFilesRights()) {
            $this->messages[] = ' Apply files rights error';
            $result           = false;
        }
    } catch (Throwable $exception) {
        $result         = false;
        $this->messages[] = $exception->getMessage();
    }

    return $result;
}
```

### **activateLicense**

This function we use only for commercial modules. It can add some trials to your license keys within the installation process. 

```php
/**
* Executes license activation only for commercial modules
*
* @return bool result of license activation
*/
public function activateLicense(): bool
{
    $lic = PbxSettings::getValueByKey('PBXLicense');
    if (empty($lic)) {
        $this->messges[] = 'License key not found...';
        return false;
     }
     // Add trial license for module with an id equal 3
     $this->license->addtrial('3');
     return true;
}
```

You can skip this procedure if you make a non-commercial module or read more information about licensing [here](../marketplace/licensing.md).

### **installFiles**

This ****function ****we use for making copies of some files or folders and create symlinks for module's files. If your module doesn't have anything special you can skip overriding this procedure. 

### installDB

We use this function to manage some changes with a database structure and data stored on a module database or system database.

{% hint style="info" %}
Every module has its own sqlite3 database stored in the DB folder within the module folder. 
{% endhint %}

We do not recommend you create and put any sqlite3 database files to your module distributive. The best way is to describe all your models and relationships according to [Phalcon](https://docs.phalcon.io/4.0/en/annotations#annotations) models annotation instructions. The internal procedure **createSettingsTableByModelsAnnotations** creates a sqlite3 database with all tables or modifies if you start the module upgrading process.

The next model file class describes the **m\_ModuleTemplate** table with **id** and **text\_field** __columns:

```php
<?php

namespace Modules\ModuleTemplate\Models;

use MikoPBX\Common\Models\Providers;
use MikoPBX\Modules\Models\ModulesModelsBase;
use Phalcon\Mvc\Model\Relation;

class ModuleTemplate extends ModulesModelsBase
{
    /**
     * @Primary
     * @Identity
     * @Column(type="integer", nullable=false)
     */
    public $id;

    /**
     * Text field example
     *
     * @Column(type="string", nullable=true)
     */
    public $textField;
    
    
    public function initialize(): void
    {
        $this->setSource('m_ModuleTemplate');  
        parent::initialize();
    }
}
```

After calling the **createSettingsTableByModelsAnnotations** you can manipulate module settings over the model class.

```php
public function installDB(): bool
{
    $result = $this->createSettingsTableByModelsAnnotations();
    
    if ($result) {
        $settings = ModuleTemplate::findFirst();
        if ($settings === null) {
            $settings               = new ModuleTemplate();
        }
        $settings->textField    = 'Some data';  
        $result = $settings->save();
    }

    return $result;
}
```

To register the module within the PBX system you have to call the **registerNewModule** function**.** It adds a record to the **PbxExtensionModules** table according to provided in **module.json** file information.

```php
public function installDB(): bool
{
    $result = $this->createSettingsTableByModelsAnnotations();
    if ($result) {
        $result = $this->registerNewModule();
    }
    return $result;
}
```

If you want to add a link to the module on a sidebar menu you should call the **addToSidebar** function, if you have some preferences you can override it.

Typical installDB function looks like the next example:

```php
public function installDB(): bool
{
    $result = $this->createSettingsTableByModelsAnnotations();
    if ($result) {
        $result = $this->registerNewModule();
    }
    if ($result) {
        $result = $this->addToSidebar();
    }
    return $result;
}
```

### fixFilesRights

This function changes files and folder ownerships and applies special executable rules for binary files and PHP-AGI scripts. 



## Uninstall module procedure

