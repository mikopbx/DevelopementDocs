---
description: >-
  This class helps to make new features, REST API methods, interacts with PBX
  core system
---

# Module main class

On the template repository, you can find an example in the file ModuleTemplate/Lib/**TemplateConf.php**  
You can use it for your project according to your preferences. The name of this class should be consist of a module unique identifier and ends with the **Conf** word.

Good examples:

* MyFirstModuleConf
* MikoCallTrackingConf
* TheBestModuleConf

Non-workable examples:

* TemplateConf
* MyFirstModuleConf1
* TheBestModuleConfig

![The main MikoPBX  module configuration class](../.gitbook/assets/nbscreenshot-2021-02-15-at-17.23.03.png)

After applying [the instructions](template-module-structure.md) your config class will be renamed automatically. 

## Asterisk configs generators

#### generateConfig

```php
/**
 * Generates core modules config files with cli messages before and after generation
 */
public function generateConfig(): void;
```

### extensions.conf

#### extensionGlobals

The example:

```php
/**
 * Prepares additional parameters for [globals] section 
 * in the extensions.conf file
 *
 * @return string
 */
public function extensionGlobals(): string;
```

#### extensionGenContexts

The example:

```php
/**
 * Prepares additional contexts sections in the extensions.conf file
 *
 * @return string
 */
public function extensionGenContexts(): string;
```



#### getIncludeInternal

The example:

```php
/**
 * Prepares additional includes for [internal] context section in the extensions.conf file
 *
 * @return string
 */
public function getIncludeInternal(): string;
```

#### extensionGenInternal

The example:

```php
/**
 * Prepares additional rules for [internal] context section in the extensions.conf file
 *
 * @return string
 */
public function extensionGenInternal(): string;
```

#### getIncludeInternalTransfer

The example:

```php
/**
 * Prepares additional includes for [internal-transfer] context section in the extensions.conf file
 *
 * @return string
 */
public function getIncludeInternalTransfer(): string;
```

#### extensionGenInternalTransfer

The example:

```php
/**
 * Prepares additional rules for [internal-transfer] context section in the extensions.conf file
 *
 * @return string
 */
public function extensionGenInternalTransfer(): string;
```

#### extensionGenHints

The example:

```php
/**
 * Prepares additional hints for [internal-hints] context section in the extensions.conf file
 *
 * @return string
 */
public function extensionGenHints(): string;
```

#### generateIncomingRoutBeforeDial

The example:

```php
/**
 * Prepares additional parameters for each incoming context for each incoming route before dial in the
 * extensions.conf file
 *
 * @param string $rout_number
 *
 * @return string
 */
public function generateIncomingRoutBeforeDial(string $rout_number): string;
```

#### generateIncomingRoutAfterDialContext

The example:

```php
/**
 * Prepares additional parameters for each incoming context
 * and incoming route after dial command in an extensions.conf file
 *
 * @param string $uniqId
 *
 * @return string
 */
public function generateIncomingRoutAfterDialContext(string $uniqId): string;
```

#### generatePublicContext

The example:

```php
/**
 * Prepares additional parameters for [public-direct-dial] section in the extensions.conf file
 *
 * @return string
 */
public function generatePublicContext(): string;
```

#### generateOutRoutContext

The example:

```php
/**
 * Prepares additional parameters for each outgoing route context
 * before dial call in the extensions.conf file
 *
 * @param array $rout
 *
 * @return string
 */
public function generateOutRoutContext(array $rout): string;
```

#### generateOutRoutAfterDialContext

The example:

```php
/**
 * Prepares additional parameters for each outgoing route context
 * after dial call in the extensions.conf file
 *
 * @param array $rout
 *
 * @return string
 */
public function generateOutRoutAfterDialContext(array $rout): string;
```

### managers.conf

#### generateManagerConf

The example:

```php
/**
 * Prepares additional AMI users data in the manager.conf file
 *
 * @return string
 */
public function generateManagerConf(): string;
```



### pjsip.conf

#### generatePeersPj

The example:

```php
/**
 * Prepares additional peers data in the pjsip.conf file
 *
 * @return string
 */
public function generatePeersPj(): string;
```

#### generatePeerPjAdditionalOptions

The example:

```php
/**
 * Prepares additional pjsip options on endpoint section in the pjsip.conf file for peer
 *
 * @param array $peer information about peer
 *
 * @return string
 */
public function generatePeerPjAdditionalOptions(array $peer): string;

```

#### overrideProviderPJSIPOptions

The example:

```php
/**
 * Override pjsip options for provider in the pjsip.conf file
 *
 * @param string $uniqid  the provider unique identifier
 * @param array  $options list of pjsip options
 *
 * @return array
 */
public function overrideProviderPJSIPOptions(string $uniqid, array $options): array;


```

#### overridePJSIPOptions

The example:

```php
/**
 * Override pjsip options for peer in the pjsip.conf file
 *
 * @param string $extension the endpoint extension
 * @param array  $options   list of pjsip options
 *
 * @return array
 */
public function overridePJSIPOptions(string $extension, array $options): array;
```



### features.conf

#### getFeatureMap

The example:

```php
/**
 * Prepares additional parameters for [featuremap] section in the features.conf file
 *
 * @return string returns additional Star codes
 */
public function getFeatureMap(): string;
```

### Other

```php
/**
 * Prepares settings dataset for a PBX module
 */
public function getSettings(): void;


/**
 * Returns the messages variable
 *
 * @return array
 */
public function getMessages(): array;

/**
 * Returns models list of models which affect the current module settings
 *
 * @return array
 */
public function getDependenceModels(): array;
```

## REST API Core generators

#### getPBXCoreRESTAdditionalRoutes

```php
/**
 * Returns array of additional routes for PBXCoreREST interface from module
 * [ControllerClass, ActionMethod, RequestTemplate, HttpMethod, RootUrl, NoAuth ]
 *
 * @return array
 * @example
 *  [[GetController::class, 'callAction', '/pbxcore/api/backup/{actionName}', 'get', '/', false],
 *  [PostController::class, 'callAction', '/pbxcore/api/backup/{actionName}', 'post', '/', false]]
 */
public function getPBXCoreRESTAdditionalRoutes(): array;


```

#### moduleRestAPICallback

```php
/**
 * Process PBXCoreREST requests under root rights
 *
 * @param array $request GET/POST parameters
 *
 * @return \MikoPBX\PBXCoreREST\Lib\PBXApiResult
 */
public function moduleRestAPICallback(array $request): PBXApiResult;
```

## System generators

### System events

#### onAfterPbxStarted

```php
/**
 * The callback function will execute after PBX started.
 */
public function onAfterPbxStarted(): void;
```

#### onBeforeModuleEnable

```php
/**
 * Process before enable action in web interface
 *
 * @return bool
 */
public function onBeforeModuleEnable(): bool;
```

#### onAfterModuleEnable

```php
/**
 * Process after enable action in web interface
 *
 * @return void
 */
public function onAfterModuleEnable(): void;
```

#### onBeforeModuleDisable

```php
/**
 * Process before disable action in web interface
 *
 * @return bool
 */
public function onBeforeModuleDisable(): bool;
```

#### onAfterModuleDisable

```php

/**
 * Process after disable action in web interface
 *
 * @return void
 */
public function onAfterModuleDisable(): void;
```

#### modelsEventChangeData

```php
/**
 * This method calls in the WorkerModelsEvents after receive each models change
 *
 * @param $data
 */
public function modelsEventChangeData($data): void;
```

#### modelsEventNeedReload

```php
/**
 * This method calls in the WorkerModelsEvents after finished process models changing
 *
 * @param array $modified_tables list of modified models
 */
public function modelsEventNeedReload(array $modified_tables): void;
```

### Core workers

#### getModuleWorkers

```php
/**
 * Returns array of workers classes for WorkerSafeScripts
 *
 * @return array
 */
public function getModuleWorkers(): array;
```

### Crond

#### createCronTasks

```php
/**
 * Adds crond rules
 *
 * @param $tasks
 */
public function createCronTasks(&$tasks): void;
```

### Iptables

#### getDefaultFirewallRules

```php


/**
 * Returns array of additional firewall rules for module
 *
 * @return array
 */
public function getDefaultFirewallRules(): array;
```

### Fail2Ban

#### generateFail2BanJails

```php

/**
 * Generates additional fail2ban jail conf rules
 *
 * @return string
 */
public function generateFail2BanJails(): string;
```

### Nginx

#### createNginxLocations

```php
/**
 * Create additional Nginx locations from modules.
 *
 */
public function createNginxLocations(): string;
```

