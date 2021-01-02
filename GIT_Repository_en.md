## Introduction

All GIT repositories are hosted on GitHub and are published by the
[github.com/AdventurePHP](https://github.com/AdventurePHP) organisation.

## Participate

The APF team is looking forward to your contributions no matter if code,
dokumentation, and ideas for any kind of improvement. To submit your
change/request, you can use the following tools:

-   Pull request of your fork of
    [github.com/AdventurePHP](https://github.com/AdventurePHP).
-   New
    [Tracker](http://tracker.adventure-php-framework.org/bug_report_page.php)
    entry.
-   Post within our [Forum](http://forum.adventure-php-framework.org).
-   E-mail an
    [APF-Team](http://adventure-php-framework.org/Seite/149-Team)
    member.

## Repositories

Under [github.com/AdventurePHP](https://github.com/AdventurePHP) there
are five GIT repositories. They are used as follows:

-   [code](https://github.com/AdventurePHP/code): Contains the code of
    the framework.
-   [config](https://github.com/AdventurePHP/config): Contains sample
    configuration files for the conponents contained in the **code**
    repository.
-   [examples](https://github.com/AdventurePHP/examples): Contains
    various example implementations that can both be used for
    demonstration of as basis for your development (especially
    [Sandbox](http://adventure-php-framework.org/Seite/074-Sandbox)).
-   [docs](https://github.com/AdventurePHP/docs): This repository has
    been created to manage the documentation and the APF web site
    sources.
-   [tools](https://github.com/AdventurePHP/tools): Hosts build scripts
    for an APF build and further tools.

## Branches

### Overview

Repositories

-   **code**
-   **config**
-   **examples**

in parallel (this is important for creating release artefacts!) have the
following branches created:

| Name                                           | Description                                                                                                                                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **master**                                     | This branch is used to integrate the changes for the next release version. Potential feature branches are merged into this branch after completion.                                                                                 |
| **release-\*** (z.B. *release-2.0*)            | Release branches are used to deliver stable releases and are dedicated to code maintenance of e.g. service packs or hotfix releases. Changes are merged to *master*.                                                                |
| **feature-\*** (z.B. *feature-2.0-namespaces*) | Feature branches are used to develop features already before the *master* branche is approved for release development. Another use case is development of features or proposals that are subject to feedback from other developers. |

The branching concept is derived from article [A successful Git
branching
model](http://nvie.com/posts/a-successful-git-branching-model/). The
above list only shows branches that are publicly available on GitHub. Of
course, each developer has it's own local branches for feature
development.

### Existing branches

The following list lists all existing branches and their relevance and
use case:

| Name                   | Description                                                           |
|------------------------|-----------------------------------------------------------------------|
| master                 | Feature development for Release 2.1.                                  |
| release-1.10           | Release and bug fixing for Release 1.10.                              |
| release-1.11           | Release and bug fixing for Release 1.11.                              |
| release-1.12           | Release and bug fixing for Release 1.12.                              |
| release-1.13           | Release and bug fixing for Release 1.13.                              |
| release-1.14           | Release and bug fixing for Release 1.14.                              |
| release-1.15           | Release and bug fixing for Release 1.15.                              |
| release-1.16           | Release and bug fixing for Release 1.16.                              |
| release-1.17           | Release and bug fixing for Release 1.17.                              |
| release-2.0            | Release and bug fixing for Release 2.0/2.0.X.                         |
| release-2.1            | Release and bug fixing for Release 2.1.                               |
| feature-1.13-form      | Feature branch for Release 1.13.                                      |
| feature-2.0-gorm       | Feature branch for GORM changes in Release 2.0.                       |
| feature-2.0-namespaces | Feature branch for introduction of namespaces in Release 2.0.         |
| feature-2.2-parser-poc | Feature branch for introduction of the new APF parser in Release 2.2. |

## Release artefacts

Out of branches

-   **code**
-   **config**
-   **examples**

the following release artefacts are being created:

| Name                                  | Description                                                                                                                                    |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| apf-codepack-<version>-php5.\*        | Contains the code base of the APF incl. all necessary migration scripts (in case required for the current version).                            |
| apf-configpack-<version>-noarch.\*    | Contains sample configuration file matching the current version.                                                                               |
| apf-demopack-<version>-php5.\*        | Contains the [Sandbox](http://adventure-php-framework.org/Seite/074-Sandbox) as sample application and development platfrm for custom modules. |
| apf-calc-example-<version>-php5.\*    | Delivers the calculator sample implementation based on the APF.                                                                                |
| apf-modules-example-<version>-php5.\* | Shows how dynamic modules can be realized for UIs.                                                                                             |
| apf-vbc-example-<version>-php5.\*     | Code belonging to the [View-based Caching](http://adventure-php-framework.org/Seite/086-View-based-caching-ein-HOWTO) article.                 |

## Using the GIT version

Changes and bug fixes of issues within the
[Tracker](http://tracker.adventure-php-framework.org/roadmap_page.php)
are pushed to branches *master* or *release-\** on GitHub. In case you
want to directly use them there are two options to update your local
project with the published changes.

The following chapter describes how to update your local version.

### Snapshot

The simplest way to use the latest changes are snapshot versions. In
case you don't need the history of changes you can download the snapshot
release matching your version under [Get
Latest](http://adventure-php-framework.org/). Snapshots are updated once
per hour.

### Clone

Using a clone of one of the APF repositories (mostly **code**) you are
provided wit the complete history of changes. You can use it as basis
for your APF-based project.

To use this option your project must comply with the following
conditions:

-   APF code files reside in a separate folder and are separated from
    the rest of your application. Example: your application is hosted in
    folder *APP* and APF sources are stored in the *APF* folder in
    parallel to the latter one. Hints can be found in chapter [Adaption
    of the base
    path](http://adventure-php-framework.org/Page/154-Class-loading#Chapter-4-1-Adaption-of-the-base-path)
    or in tutorial [My first
    website](http://adventure-php-framework.org/Page/048-My-first-website#Chapter-3-2-Creating-the-application-structure).
-   Configuration files for APF components are stores in a separate
    folder that is independent from the *APF* folder. Example: APF
    sources are stored under *APF* all related configuration files under
    *config/APF*. Hints can be found in chapter [Adaption of the
    configuration base
    path](http://adventure-php-framework.org/Page/154-Class-loading#Chapter-4-2-Adaption-of-the-configuration-base-path)
    or in tutorial [My first
    website](http://adventure-php-framework.org/Page/048-My-first-website#Chapter-3-2-Creating-the-application-structure).

In case your project complys with the above structure you may want to
use Git's [Submodule](http://git-scm.com/docs/git-submodule) feature to
create a clone of the **code** repository within *APF*:

``` bash
cd /path/to/project
mkdir APF && cd APF
git clone https://github.com/AdventurePHP/code.git .
```

<languages />