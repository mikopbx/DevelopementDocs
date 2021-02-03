---
description: How to make your own module from MikoPBX module template
---

# Prepare the module structure

On our public repository you can find [ModuleTemplate](https://github.com/mikopbx/ModuleTemplate), we advice to use it create the future module structure.

Every MikoPBX module must have _unique_ _identifier_, i.e. you are developping a call back module with identifier **ModuleCalbackFromWebsite**

Go to your developement root folder and execute the next bash script: 

{% tabs %}
{% tab title="Bash" %}
```bash
cd ~/PhpstormProjects
curl -s https://raw.githubusercontent.com/mikopbx/ExtensionsDevTools/master/create_module.sh | bash /dev/stdin ModuleCalbackFromWebsite
```
{% endtab %}
{% endtabs %}

It clones the **ModuleTemplate** repository and renames folders, files, namespaces and classnames according to the new module uniqueid **ModuleCalbackFromWebsite.**

Next you should load **mikopbx/core** package and dependent libraries

```text
cd ~/PhpstormProjects/ModuleCalbackFromWebsite
composer install
```

Next you van create git repository for new module and commit all new code. 

```text
git init
echo "vendor/" > .gitignore
git add .
git commit -m 'initial commit'
```

If you have remore git repository you can push your module into it.

```text
git remote add origin <url>
git push -u origin master
```

Now you can create zip arhive and install your new module on MikoPBX server.



