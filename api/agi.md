---
description: MikoPBX describes its own implementation of the PHP-AGI library.
---

# AGI

The source code is described by the [link](https://github.com/mikopbx/Core/blob/108b6ffa06faf19d39770a4af505166709838222/src/Core/Asterisk/AGI.php#L31-L31). To start using, be sure to connect '`Globals.php'`

Example of a simple PHP script:

```php
<?php

use MikoPBX\Core\Asterisk\AGI;
require_once 'Globals.php';

$agi = new AGI();
$agi->Answer();
$agi->exec_goto('internal', '2001', '1');
```
