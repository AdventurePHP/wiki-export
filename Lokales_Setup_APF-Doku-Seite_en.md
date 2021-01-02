This tutorial shows how to setup the APF web page on your local machine.

## GIT check-out

All sources of the APF web page are managed on
[GitHub.com/docs](https://www.github.com/AdventurePHP/docs). In
addition, you'll need the content of the
[code](https://www.github.com/AdventurePHP/docs) repository matching the
state of the previous one. To get the content of the *code* respository
a submodule configuration has been added that points to the current
development line. To use it, please initialize the link on your local
box.

To get started, please execute

``` bash
mkdir -p /var/www/html/docu-page && cd /var/www/html/docu-page
git clone https://www.github.com/AdventurePHP/docs.git .
```

You'll now have the sources of the APF web page available.

As a second step, initialize the code submodule as follows:

``` bash
git submodule init
```

In case you already have a clone of the **code**- repository GIT will
try to leverage it as remote repository. To workaround that you can call
the following command in */var/www/html/docu-page/APF*:

``` bash
git remote set-url origin https://github.com/AdventurePHP/code.git
```

Now, the sources of the framework als available on your local machine
and it's time to proceed with the following chapters.

## Create VHOSTs

Running the web site a virtual hosts is necessary:

apf.org (or similar)

Domain *apf.org* may be exchanged with any desired name.

The subsequent code box shows the necessary VHOSTs (assuming the
check-out folder is */var/www/html/docu-page*):

``` apache
<VirtualHost *:80>
    DocumentRoot /var/www/html/docu-page
    ServerName apf.org
    AllowEncodedSlashes On
    ...
</VirtualHost>

== Adapt .htaccess ==

The SVN sources include a wizard to setup the APF web page on your local machine. In order to call it under ''http://apf.org/setup_wizard.php'' please adapt the ''.htaccess'' file and remove the following lines:

<source lang="apache">
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
```

In addition, please create folder */var/www/html/docu-page/files*. This
releases folder normally contains the APF releases.

## Execution of the wizard

Now, you can complete all steps offered by the wizard.

The wizard intentionally offers several steps to give freedom to execute
them separately. This is especially important for the search index.

### Adaption if the index.php

The *index.php* needs to be adapted to your local setup. For this
reason, please call *<http://apf.org/setup_wizzard.php?page=index>* and
provide the releases folder.

### Database configuration

Now, the database configuration can be created. Wizard
*<http://apf.org/setup_wizard.php?page=database>* assumes that all
connections are handled via the same user. In case this is not true for
your local setup pleaese adapt the configuration file manually after
execution of the wizard.

For further instructions, please consult the wizard.

### Database initialization

In order to run the application locally all databases must be
initialized. For this reason, please refer to wizard
*<http://apf.org/setup_wizard.php?page=setup>*. You can either
initialize databases separately or all at once.

Multiple executions is no issue. No data will be lost since tables are
only created if they are not there.

For further instructions, please consult the wizard.

### Search index creation

The seach index takes two responsibilities: URL generation and basis of
the on-page search. The URL generation uses the article list within the
search index.

To import articles or create the search index, please call wizard
*<http://apf.org/setup_wizard.php?page=search>*. Do this whenever you
created a new article.

For further instructions, please consult the wizard. <languages />