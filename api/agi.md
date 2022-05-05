---
description: MikoPBX describes its own implementation of the PHP-AGI library.
---

# AGI

The source code is described by the [link](https://github.com/mikopbx/Core/blob/108b6ffa06faf19d39770a4af505166709838222/src/Core/Asterisk/AGI.php#L31-L31). To start using, be sure to connect '`Globals.php'`

It is better to place all files related to your project in the directory `/storage/usbdisk1/mikopbx/custom_modules/your directory`, this directory will be added to the backup when using the [Backup module](https://github.com/mikopbx/ModuleBackup).

## Example of a simple PHP script:

```php
<?php

use MikoPBX\Core\Asterisk\AGI;
require_once 'Globals.php';

$agi = new AGI();
$agi->Answer();
$agi->exec_goto('internal', '2001', '1');
```

## Get extension state

Example of getting the extension status. In some cases, it is necessary to understand whether an internal number exists and what its state is.

```php
<?php

function getExtensionStatus($number): int
{
    global $agi;
    $state   = $agi->get_variable("DEVICE_STATE(PJSIP/$number)", true);
    $dExists = $agi->get_variable("DIALPLAN_EXISTS(internal,$number,1)", true);
    $this->Verbose("DEVICE_STATE: {$state} DIALPLAN_EXISTS: $dExists");

    $stateTable = [
        'UNKNOWN'       => ['Status'=> -1, 'StatusText' => 'Unknown'],
        'INVALID'       => ['Status'=> -1, 'StatusText' => 'Unknown'],
        'NOT_INUSE'     => ['Status'=> 0, 'StatusText' => 'Idle'],
        'INUSE'         => ['Status'=> 1, 'StatusText' => 'In Use'],
        'BUSY'          => ['Status'=> 2, 'StatusText' => 'Busy'],
        'UNAVAILABLE'   => ['Status'=> 4, 'StatusText' => 'Unavailable'],
        'RINGING'       => ['Status'=> 8, 'StatusText' => 'Ringing'],
        'ONHOLD'        => ['Status'=> 16, 'StatusText' => 'On Hold'],
    ];

    if($state === 'INVALID' && $dExists === '1'){
        $result = $stateTable['NOT_INUSE'];
    }else{
        $result = $stateTable[$state]??$stateTable['UNKNOWN'];
    }
    return $result;
}
```

## Example of a Number input request (IVR)

```php
<?php

use MikoPBX\Core\Asterisk\AGI;
require_once 'Globals.php';

$ivr_menu    = '/storage/usbdisk1/mikopbx/custom_modules/your directory/file name without extension';
$result      = $agi->getData($ivr_menu, 3000, 4);
$selectednum = $result['result'];

$agi->exec(
    'Dial',
    "Local/{$selectednum}@internal/n,30," . 'TtekKHhU(dial_answer)b(dial_create_chan,s,1)'
);
```

