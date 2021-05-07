---
description: >-
  This guide helps you to create new module for MikoPBX server and discover some
  internal structures
---

# MikoPBX development guide

## What is it?

MikoPBX is an open-source GUI \(graphical user interface\) that controls and manages Asterisk \(PBX\). MikoPBX is licensed under GPL. MikoPBX is an entirely modular GUI for Asterisk written in PHP7 and Javascript. Meaning you can simply write any module you can think of and distribute it free of cost to your clients so that they can take advantage of beneficial features in [Asterisk](http://www.asterisk.org/) The released firmware consists Linux operation system and all needing services like Asterisk, Nginx, PHP-FPM, iptables etc.

## How to start module development

Firstly you should prepare your development environment according to[ the ](prepare-ide-tools.md)[instructions](module-developement/template-module-structure.md). You will prepare your IDE, install some dependency and special tools to make your workspace well organized for the right developing process.

Next, you have to clone the template repository and rename folders, files and classes with a special script according to [these instructions](module-developement/template-module-structure.md). Finally, you will have the new module with the right folder and classes structure. 

It is ready to install on the MikoPBX and start the developing process.

## Get into the mikopbx internal structure

Before starting development you have to deep into the MikoPBX internal structure.  We provided some details about internal stricture, data models, models relationships in [the core description](core.md). 

Also, we described [the admin interface structure](admin-interface.md), you can use int as a good example of how to develop a module interface. There is a lot of JavaScript code and PHP classes that organizes an user interaction. 

Some actions require root access to system services. You can interact with them by the [MikoPBX REST API](rest-api.md).

## Modify your module

We advise starting development from a proper data structure. Every module has its own database. You should describe the structure of tables on models classes according to the Phalcon models [documentations](https://docs.phalcon.io/4.0/en/db-models). Describe every column with its type using metadata annotations and organize data relationships between other tables.

Next, you should change the installer class. It helps to prepare a real database with some data within the installation process. Read our instructions about it [here](module-developement/module-installer.md). 

The main module class helps you to interact with the MikoPBX classes. You can modify asterisk config files, add your own contexts to the extension.conf file, override any  PJSIP parameters for peers and providers, add manager API accounts, add extra rules to iptables and fail2ban service's configuration files, add extra cron tasks, organize new REST API routes, make PHP workers. Start from [the description](module-developement/module-class.md) of the main module class.

Develop and [debug](module-developement/debuging.md) your module. Ask for help from our community. You are welcome to the big family!

## Make money or provide it for free

When you finished your module you can add it to the MIKO marketplace. We will spread it around all MikoPBX's customers. 

If you want to earn some many we can add some licensing features to your module and raise money together. Read the licensing articles [here](marketplace/licensing.md).

