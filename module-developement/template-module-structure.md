---
description: Clone module template and prepare it for developing
---

# How to start

### Create a new module structure

To create a new module for MikoPBX, you can use the [ModuleTemplate](https://github.com/mikopbx/ModuleTemplate) repository as ready for use template.

Every MikoPBX module must have a unique identifier, i.e. you are developing a call back module with the identifier – **ModuleCalbackFromWebsite**

Go to your development root folder and execute the next script:&#x20;

{% tabs %}
{% tab title="Linux/Mac" %}
```bash
cd ~/PhpstormProjects
curl -sL https://git.io/JtzEa | bash /dev/stdin ModuleCalbackFrom
```
{% endtab %}
{% endtabs %}

It clones the **ModuleTemplate** repository and renames folders, files, namespaces and class names according to the new module unique id – **ModuleCalbackFromWebsite.**

Now you can create a zip archive and install your new module on the MikoPBX server.

![](../.gitbook/assets/ScreenFlow.gif)

### Next steps

{% hint style="info" %}
Read the article [Prepare IDE and system tools](../prepare-ide-tools.md) before following the next instructions.
{% endhint %}

You should load the **mikopbx/core** package and dependent libraries it helps to resolve references between MikoPBX class names. &#x20;

{% tabs %}
{% tab title="Linux/Mac" %}
```
cd ~/PhpstormProjects/ModuleCalbackFromWebsite
composer install
```
{% endtab %}
{% endtabs %}

Next, you can create a git repository for a new module and commit all new code.&#x20;

{% tabs %}
{% tab title="Linux/Mac" %}
```
git init
echo "vendor/" > .gitignore
git add .
git commit -m 'initial commit'
```
{% endtab %}
{% endtabs %}

If you have a remote git repository you can push your module into it.

{% tabs %}
{% tab title="Linux/Mac" %}
```
git remote add origin <url>
git push -u origin master
```
{% endtab %}
{% endtabs %}



