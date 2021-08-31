---
description: Database tables structure and relative relationships between tables
---

# Data model

We advise starting development from a proper data structure. Every module has its own database. You should describe the structure of tables on models classes according to the Phalcon models [documentations](https://docs.phalcon.io/4.0/en/db-models). Describe every column with its type using metadata annotations and organize data relationships between other tables.

### Creating a new data model

The model files are described in the directory "**Models**":

```css
ModuleTemplate
├── agi-bin
├── App
├── bin
├── composer.json
├── db
├── Lib
├── Messages
├── Models
│   ├── ModuleTemplate.php
│   ├── PhoneBook.php
│   └── QuestionsQuality.php
├── module.json
├── public
├── README.md
├── readme.ru.md
└── Setup
```

The description of the model is reduced to the description of the metadata of the database table.

Create a file named "**QuestionsQuality.php**" and Extend from **ModulesModelsBase**:

Adding a description of the table metadata. Class properties - column names. In the comments, we describe the type of value.

```php
<?php
namespace Modules\ModuleTemplate\Models;
use MikoPBX\Modules\Models\ModulesModelsBase;

class QuestionsQuality extends ModulesModelsBase
{
    /**
     * @Primary
     * @Identity
     * @Column(type="integer", nullable=false)
     */
    public $id;
    
    /**
     * @Column(type="string", nullable=true)
     */
    public $question;
    
    /**
     * @Column(type="integer", nullable=true)
     */
    public $priority = '0';
    
    /**
     * @Column(type="integer", nullable=true)
     */
    public $disabled;
    
    public function initialize(): void
    {
        $this->setSource('m_QuestionsList');
        parent::initialize();
        $this->useDynamicUpdate(true);
    }

}
```

In this example, we have described the following fields:

* **id -** Required fields, primary key \(type **integer**\).
* **question** - Field for the description of the question text \(type string\)
* **priority** - Required fields for ordering questions
* **disabled** - boolean, allows you to mark the question as irrelevant, **note that the boolean is described as integer \(0 or 1\)**

The function "**initialize**" describes the name of the table "**m\_QuestionsList**" in module  the database.

### Requirements

* The table name "**m\_QuestionsList**" must be unique within the module
* The class name "**QuestionsQuality**" must be unique within the module

### Using the model

After describing the model, you need to package the module in a ZIP archive and re-install it. The table will be created only after the installation is completed.

The module database \(**sqlite3**\) will be created using the following path:

`/storage/usbdisk1/mikopbx/custom_modules/ModuleTemplate/db/module.db`

To check for a new table, run:

`sqlite3 /storage/usbdisk1/mikopbx/custom_modules/ModuleTemplate/db/module.db .tables`

* where "**ModuleTemplate**" is the name of the module



