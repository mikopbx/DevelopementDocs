---
description: How to organize workspace for MikoPBX extension developement.
---

# Prepare IDE and system tools

## Environment



We widely use the **composer** to manage dependents libraries, **NodeJS** runtime for Javascript code processing.

{% tabs %}
{% tab title="Mac" %}
```bash
# XCode Command Line Tools
xcode-select --install

# Install Homebrew
/bin/bash -c "$(curl -fsSL https://git.io/JIY6g)"

# Install Composer
brew install composer

# Install Node package manager
brew install node

# Install AirBnb linter
npx install-peerdeps --dev eslint-config-airbnb-base

# Install Babel toolchain
npm install --save-dev @babel/core @babel/cli

# Install a babel preset for transforming JavaScript for Airbnb 
npm install --save-dev babel-preset-airbnb@^3.0.1

# Install php 7.4
brew install php@7.4
brew unlink php && brew link --overwrite --force php@7.4
```
{% endtab %}

{% tab title="Windows" %}
```
# Install PHP 7.4
# https://dev.to/amulya_shahi/how-to-download-install-php-7-4-6-manually-on-windows-10-4io0

# Install GIT
# Just go to https://git-scm.com/download/win and the download will start automatically. 

# Install composer
# Download and run https://getcomposer.org/Composer-Setup.exe

# Install nodeJS
# Download and run https://nodejs.org/en/download/ 

# Create DIR for example H:/MikoPBXUtils, enter to it

# Install AirBnb linter
npx install-peerdeps --dev eslint-config-airbnb-base

# Install Babel toolchain
npm install --save-dev @babel/core @babel/cli

# Install a babel preset for transforming JavaScript for Airbnb 
npm install --save-dev babel-preset-airbnb@^3.0.1
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
At this point, I strongly recommend closing **ALL your terminal tabs and windows**. This will mean opening a new terminal to continue with the next step. This is strongly recommended because some really strange path issues can arise with existing terminals (trust me, I have seen it!).
{% endhint %}

{% tabs %}
{% tab title="Mac" %}
```bash
#install phalcon
brew tap phalcon/extension https://github.com/phalcon/homebrew-tap
brew install phalcon@4.1.0 --build-from-source 
```
{% endtab %}
{% endtabs %}

### PHPStorm IDE

We advise using PHPStorm IDE because all MikoPBX code was written with this tool.

You have to download it by the next [link](https://www.jetbrains.com/phpstorm/) and install it.

Create a new **PHP empty Project from** existing sources.

![MikoPBX module structure](.gitbook/assets/screenshot-2021-02-04-at-15.24.30.png)

Setup the **composer** executable path according to this [manual](https://www.jetbrains.com/help/phpstorm/composer-page.html).
