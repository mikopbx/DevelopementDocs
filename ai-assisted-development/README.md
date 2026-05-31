---
description: >-
  Using the /mikopbx-module AI skill to create, augment and optimize modules.
---

# Overview

MikoPBX ships with an AI skill, **`/mikopbx-module`**, that turns a plain-language
description into a complete, convention-correct module. Instead of hand-copying
boilerplate from an example module, you describe what you want and the skill runs a
structured discovery dialog, selects the right integration recipes, generates the full
file tree from canonical templates and EXAMPLES, and self-checks the result.

This chapter explains what the skill is, the four modes it operates in, how to activate
it, and what guarantees it makes about the code it emits.

{% hint style="info" %}
The skill is a development-time tool. It runs in your AI coding environment (it has
`Read`, `Write`, `Edit`, `Grep`, `Glob` and `Bash` access) and writes source files into
your checkout. It does **not** run inside MikoPBX itself, and nothing it generates depends
on the skill being present at runtime — the output is ordinary module code you own.
{% endhint %}

## Target platform

The skill targets the current MikoPBX baseline and emits no legacy shims:

```
PHP:             8.4
min_pbx_version: 2025.1.1
Framework:       Phalcon 5.9.3
Legacy:          none (no MikoPBXVersion.php, no Phalcon 4 / PHP 7 patterns)
```

Generated code uses PHP 8.4 features — typed properties, constructor promotion, `match`
expressions, named arguments and enums — except for Phalcon ORM model column properties,
which follow the SQLite/Phalcon convention (untyped `$id`, nullable string columns). The
skill knows the difference and applies each rule in the right place.

## The four modes

The skill recognizes intent from your message and works in one of four modes.

### 1. Create New Module

Generates a brand-new module from a description. This is a four-phase pipeline:

1. **Discovery dialog** — the skill parses your description, proposes a module name
   (`Module{Feature}`), asks where it should live (`Extensions/` for production modules or
   `Extensions/EXAMPLES/{Category}/` for learning modules), and proposes a set of recipes.
2. **Generation** — files are emitted in dependency order (`module.json`,
   `Setup/PbxExtensionSetup.php`, models, the `{Feature}Conf` config class, then UI / REST /
   worker / AGI files as the chosen recipes require).
3. **Post-generation checks** — `php -l` on every generated PHP file, Babel transpilation of
   JavaScript, and a `module.json` validity check.
4. **Report** — a summary of files created, recipes applied, check results, and next steps.

### 2. Augment Existing Module

Adds functionality to a module that already exists (for example, "add a REST API" or "add a
background worker"). The skill first reads the existing tree, identifies which recipes are
already present and which hooks the config class already uses, plans the new and modified
files, and then applies the changes — extending the config class with new hook methods
without breaking the ones already there.

### 3. Optimize Module

Scans an existing module for anti-patterns, reports findings with severity and a suggested
fix for each, and — with your approval — applies the fixes to bring the code in line with
the reference standards.

### 4. Consultation

Answers architecture and how-to questions about the MikoPBX module system without writing
files — for example, "which hook fires when the dialplan is rebuilt?" or "where should a
periodic task live?". Use this mode to plan before you build.

## Recipes

A *recipe* is a self-contained slice of module functionality. The `base` recipe is always
applied; the rest are selected automatically from signals in your description (and confirmed
with you before generation).

| Recipe     | What it adds                                              |
| ---------- | --------------------------------------------------------- |
| `base`     | The minimal installable module (always applied)           |
| `ui`       | Admin settings page: controller, form, Volt view, JS, CSS |
| `rest-api` | Versioned REST API controllers and actions                |
| `dialplan` | Call-routing / dialplan integration hooks                 |
| `agi`      | AGI scripts (CallerID lookup, pre-dial logic)             |
| `workers`  | Background workers (queues, AMI/event listeners)          |
| `firewall` | Firewall ports and fail2ban rules                         |
| `acl`      | Access-control roles and permissions                      |
| `system`   | Scheduled tasks (cron) and system service config          |

See [Module recipes](../module-developement/recipes.md) for the full specification of each
recipe and the files it produces.

## Activation phrases

The skill activates on natural-language requests in Russian or English, or on an explicit
`/mikopbx-module` invocation:

{% tabs %}
{% tab title="English" %}
* `Create module for ...` / `Generate module ...`
* `Add to module ModuleBlackList ...`
* `Optimize module ModuleBlackList`
* `How to make module ...` / `Improve module ...`
* `/mikopbx-module ...`
{% endtab %}

{% tab title="Русский" %}
* `Создай модуль для ...` / `Сгенерируй модуль ...`
* `Добавь в модуль ModuleBlackList ...`
* `Оптимизируй модуль ModuleBlackList`
* `Как сделать модуль ...` / `Доработай модуль ...`
* `/mikopbx-module ...`
{% endtab %}
{% endtabs %}

## What to expect from a session

The running example throughout this guide is a fictional spam-blocking module,
**ModuleBlackList** (config class `BlackListConf`, main class `BlackListMain`, model
`BlackListNumbers` backed by table `m_BlackListNumbers`, front-end script
`module-black-list.js`). A "create" session for it might begin:

```
You:   Create a module that blocks incoming calls from a blacklist of numbers,
       with a settings page and a REST API to manage the list.

Skill: Proposed name: ModuleBlackList. Where should it live —
       Extensions/ (production) or Extensions/EXAMPLES/ (example)?
       Recipes I plan to apply: base, ui, rest-api, dialplan.
       A few questions before I generate:
         - Should blocked numbers be stored in the database? (suggests model
           BlackListNumbers / table m_BlackListNumbers)
         - Should the block happen on the incoming dialplan, or via an AGI lookup?
         - Does the REST API need full CRUD, or read-only?
```

{% hint style="warning" %}
**The skill asks a lot of questions and waits for plan approval before it writes anything.**
This is by design — clarifying intent up front is cheaper than regenerating a wrong tree.
Expect a discovery dialog, then a proposed plan (name, location, recipes, file list). The
skill generates only after you approve that plan.
{% endhint %}

When generation completes, the skill reports the created files and the results of its
self-checks (PHP lint, Babel, `module.json` validation) so you can review before installing.

## How the code is grounded

The skill does not hallucinate module structure. Before emitting code it studies the
canonical example modules shipped in the repository and mirrors their patterns:

* REST API (current version): `Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv3/`
* Web UI settings page: `Extensions/EXAMPLES/WebInterface/ModuleExampleForm/`
* AMI / background workers: `Extensions/EXAMPLES/AMI/ModuleExampleAmi/`
* Earlier REST API styles: `Extensions/EXAMPLES/REST-API/ModuleExampleRestAPIv{1,2}/`

These are the same reference modules documented elsewhere in this guide, so the generated
code looks like — and links cleanly against — the rest of the MikoPBX module ecosystem.

## Next steps

* [Using the skill](using-the-skill.md) — the discovery-to-approval workflow, mode by mode,
  with the prompts that drive each one.
* [What it generates](what-it-generates.md) — the file tree, the per-recipe outputs, and the
  self-checks the skill runs before it hands the module back to you.
