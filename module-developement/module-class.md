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

Generates core modules config files with cli messages before and after generation.

Example:

```php
public function generateConfig(): void;
```

### extensions.conf

#### extensionGlobals

Prepares additional parameters for \[globals\] section in extensions.conf file

Example:

```php
public function extensionGlobals(): string;
```

#### extensionGenContexts

Prepares additional contexts sections in extensions.conf file

Example:

```php
public function extensionGenContexts(): string;
```

#### getIncludeInternal

Prepares additional includes for \[internal\] context section in the extensions.conf file

Example:

```php
public function getIncludeInternal(): string;
```

#### extensionGenInternal

Prepares additional rules for \[internal\] context section in the extensions.conf file

Example:

```php
public function extensionGenInternal(): string;
```

#### getIncludeInternalTransfer

Prepares additional includes for \[internal-transfer\] context section in the extensions.conf file

Example:

```php
public function getIncludeInternalTransfer(): string;
```

#### extensionGenInternalTransfer

Prepares additional rules for \[internal-transfer\] context section in the extensions.conf file

Example:

```php
public function extensionGenInternalTransfer(): string;
```

#### extensionGenHints

Prepares additional hints for \[internal-hints\] context section in the extensions.conf file

Example:

```php
public function extensionGenHints(): string;
```

#### generateIncomingRoutBeforeDial

Prepares additional parameters for each incoming context for each incoming route before dial in extensions.conf file

Example:

```php
public function generateIncomingRoutBeforeDial(string $rout_number): string;
```

#### generateIncomingRoutAfterDialContext

Prepares additional parameters for each incoming context \* and incoming route after dial command in an extensions.conf file

Example:

```php
public function generateIncomingRoutAfterDialContext(string $uniqId): string;
```

#### generatePublicContext

Prepares additional parameters for \[public-direct-dial\] section in the extensions.conf file

Example:

```php
public function generatePublicContext(): string;
```

#### generateOutRoutContext

Prepares additional parameters for each outgoing route context \* before dial call in the extensions.conf file

Example:

```php
public function generateOutRoutContext(array $rout): string;
```

#### generateOutRoutAfterDialContext

Prepares additional parameters for each outgoing route context  after dial call in the extensions.conf file

Example:

```php
public function generateOutRoutAfterDialContext(array $rout): string;
```

### managers.conf

#### generateManagerConf

Prepares additional AMI users data in the manager.conf file

Example:

```php
public function generateManagerConf(): string;
```



### pjsip.conf

#### generatePeersPj

Prepares additional peers data in the pjsip.conf file

Example:

```php
public function generatePeersPj(): string;
```

#### generatePeerPjAdditionalOptions

Prepares additional pjsip options on endpoint section in the pjsip.conf file for peer

Example:

```php
public function generatePeerPjAdditionalOptions(array $peer): string;
```

#### overrideProviderPJSIPOptions

Override pjsip options for provider in the pjsip.conf file

Example:

```php
public function overrideProviderPJSIPOptions(string $uniqid, array $options): array;
```

#### overridePJSIPOptions

Override pjsip options for peer in the pjsip.conf file

Example:

```php
public function overridePJSIPOptions(string $extension, array $options): array;
```

### features.conf

#### getFeatureMap

Prepares additional parameters for \[featuremap\] section in the features.conf file

Example:

```php
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

Returns array of additional routes for PBXCoreREST interface from module

 \[ControllerClass, ActionMethod, RequestTemplate, HttpMethod, RootUrl, NoAuth \]

Example:

```php
 public function getPBXCoreRESTAdditionalRoutes(): array
    {
        return [
            [
                GetController::class,
                'callAction',
                '/pbxcore/api/backup/{actionName}',
                'get',
                '/',
                false,
            ],
            [
                PostController::class, 
                'callAction', 
                '/pbxcore/api/backup/{actionName}', 
                'post', 
                '/', 
                false
            ],
        ];
    }
```

#### moduleRestAPICallback

Process PBXCoreREST requests under root rights

Example:

```php
public function moduleRestAPICallback(array $request): PBXApiResult;
```

## System generators

### System events

#### onAfterPbxStarted

The callback function will execute after PBX started

Example:

```php
public function onAfterPbxStarted(): void;
```

#### onBeforeModuleEnable

Process before enable action in web interface

Example:

```php
public function onBeforeModuleEnable(): bool;
```

#### onAfterModuleEnable

Process after enable action in web interface

Example:

```php
public function onAfterModuleEnable(): void;
```

#### onBeforeModuleDisable

Process before disable action in web interface

Example:

```php
public function onBeforeModuleDisable(): bool;
```

#### onAfterModuleDisable

Process after disable action in web interface

Example:

```php
public function onAfterModuleDisable(): void;
```

#### modelsEventChangeData

This method calls in the WorkerModelsEvents after receive each models change

Example:

```php
public function modelsEventChangeData($data): void;
```

#### modelsEventNeedReload

This method calls in the WorkerModelsEvents after finished process models changing

Example:

```php
public function modelsEventNeedReload(array $modified_tables): void;
```

### Core workers

#### getModuleWorkers

Returns array of workers classes for WorkerSafeScripts

Example:

```php
public function getModuleWorkers(): array;
```

### Crond

#### createCronTasks

Add crond tasks

Example:

```php
public function createCronTasks(&$tasks): void;
```

### Iptables

#### getDefaultFirewallRules

Returns array of additional firewall rules for module

Example:

```php
public function getDefaultFirewallRules(): array;
```

### Fail2Ban

#### generateFail2BanJails

Generates additional fail2ban jail conf rules

Example:

```php
public function generateFail2BanJails(): string;
```

### Nginx

#### createNginxLocations

Create additional Nginx locations from modules

Example:

```php
public function createNginxLocations(): string;
```

