---
description: Clone module template and prepare it for developing
---

# How to start

### Try to install it on MikoPBX

Now you can create a zip archive and install your new module on the MikoPBX server.

![](../.gitbook/assets/ScreenFlow.gif)

### Next steps

{% hint style="info" %}
Read the article [Prepare IDE and system tools](../prepare-ide-tools/) before following the next instructions.
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



