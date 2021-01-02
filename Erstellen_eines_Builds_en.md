On this page all APF build scripts are documented. Further down, you'll
find a HOWTO creating builds.

## Preparation

### Windows

Unser Windows build scripts can only be executed under Cygwin since they
are written in Bash. Please install the following components:

-   Cygwin (Instructions and download under
    [cygwin.org](http://cygwin.org)).
-   doxygen + graphviz (Instructions and download under
    [sourceware.org/cygwinports](https://sourceware.org/cygwinports)).

### LINUX

Under LINUX you can install the required packages with the packet
manager of your distribution. Please install the following packages:

-   Git (e.g. *git-1.9.0-1.fc20.i686* under Fedora 20)
-   Doxygen (e.g. *doxygen-1.8.6-1.fc20.i686* under Fedora 20)
-   Graphviz / dot (e.g. *graphviz-2.34.0-8.fc20.i686* under Fedora 20)

## Snapshot release

As described under \[GIT Repository\] snapshot release are created every
hour for the branches configured. This mechanism uses the
[create-git-snapshot.sh](https://github.com/AdventurePHP/tools/blob/master/build/snapshot/create-git-snapshot.sh)
script to prepare all contents for publication.

The signature is as follows:

``` bash
$ ./create-git-snapshot.sh
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF snapshot                                                     ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 12:12:07
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Not enough parameters. Aborting!
Usage: ./create-git-snapshot.sh <git-branch> <release-version>

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 12:12:07
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

In case you want to create a snapshot version on your local machine,
please use the following call:

``` bash
$ ./create-git-snapshot.sh master 2.1
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF snapshot                                                     ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 11:46:59
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Creating snapshot release for version 2.1 from branch master ...
[INFO] Using workspace /cygdrive/c/Users/Christian/Entwicklung/GIT/tools/build/snapshot/workspace to preare snapshot release ...
[INFO] Clearing workspace ...
[INFO] Fetching sources from GitHub ...
[INFO] Fetching sample config from GitHub ...
[INFO] Creating TGZ file ...
[INFO] Publishing snapshot file apf-2.1-snapshot-php5.tar.gz for version 2.1 from branch master ...

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 11:47:09
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

Execution on local machines may lead to the following error during
publication of the snapshot:

``` bash
mv: cannot move `../apf-2.1-snapshot-php5.tar.gz' to `/cygdrive/***/tools/build/snapshot/../files/snapshot': No such file or directory
```

You can ignore this since the snapshot has been created within the
current directory though.

Please note, that snapshot versions can only be created from the sources
on [github.com/AdventurePHP](https://github.com/AdventurePHP). Local GIT
repositories are not supported. For this reason, snapshot releases
cannot be created on the way.

## Release build

Release builds are created manually after releasing the code base on
local machines. For this reason, ypu can use the
[create_build.sh](https://github.com/AdventurePHP/tools/blob/master/build/release/create_build.sh)
script. It offeres several options and allows to create a build from
local GIT repositories.

### Configuration

To execute the script on your local machine, a local configuration file
must be created - a build profile. The profile (a bash file) contains
the following parameters:

| Name                       | Description                                                                                                                                                          |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BUILD_PATH                | This fully-qualified path points to the folder where the release is being created. It refers to both the workspace as the storage location of the build.             |
| CODE_LOCAL_REPO_PATH    | In case a local GIT repository should be used for the [code-Repository](https://github.com/AdventurePHP/code) please specify the fully-qualified path to it.         |
| DOCS_LOCAL_REPO_PATH    | In case a local GIT repository should be used for the [docs-Repository](https://github.com/AdventurePHP/docs) please specify the fully-qualified path to it.         |
| CONFIG_LOCAL_REPO_PATH  | In case a local GIT repository should be used for the [config-Repository](https://github.com/AdventurePHP/config) please specify the fully-qualified path to it.     |
| EXAMPLE_LOCAL_REPO_PATH | In case a local GIT repository should be used for the [examples-Repository](https://github.com/AdventurePHP/examples) please specify the fully-qualified path to it. |

The configuration file can be stored at any place as it is passed as CLI
option to the build script.

Example (under Windows/cygwin):

``` bash
$ cat apf_build.conf
# local repo path to clone for build
DOCS_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/docs
CODE_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/code
CONFIG_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/config
EXAMPLE_LOCAL_REPO_PATH=/cygdrive/c/Users/Christian/Entwicklung/GIT/examples

# build environment
BUILD_PATH=/cygdrive/c/Users/Christian/Entwicklung/Build
```

### Options

The build script offers the following options to control the result:

<table>
<thead>
<tr class="header">
<th><p>Name</p></th>
<th><p>Description</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>-c</p></td>
<td><p>Fully-qualified name of the configuration file described in the above chapter.</p></td>
</tr>
<tr class="even">
<td><p>-b</p></td>
<td><p>GIT branch to be used to create the release upon. This option will be used to check out repositories <a href="https://github.com/AdventurePHP/code">code</a>, <a href="https://github.com/AdventurePHP/config">config</a>, and <a href="https://github.com/AdventurePHP/examples">examples</a>.</p></td>
</tr>
<tr class="odd">
<td><p>-v</p></td>
<td><p>Version number of the builds (z.B. <em>2.1</em>).</p></td>
</tr>
<tr class="even">
<td><p>-m (optional; Default: <em>all</em>)</p></td>
<td><p>Defines, which parts of the build will be created. Available parts are:</p>
<ul>
<li>apidocs: APF doxygen documentation.</li>
<li>codepack: Code release.</li>
<li>configpack: Example configuration files.</li>
<li>demopack: Sandbox release</li>
<li>examples: Implementation examples (vbc, modules, calc).</li>
</ul></td>
</tr>
</tbody>
</table>

In case you call the build script without any parameter or with the *-h*
option usage is shown:

``` bash
$ ./create_build.sh -h
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 12:32:04
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Usage: create_build.sh -c <config-file> -b <git branch> -v <build-version> [-m <modules-to-build>]

Please note: <git branch> must be replaced with the name of the GIT branch and <build-version> indicates the version number in the release files.
Using the -o parameter you can create a build with the sources downloaded beforehand.
The configuration file referred to must specify the following parameters:

- CODE_LOCAL_REPO_PATH (opt)   : The source location where the APF sources are checked out on local disk.
- DOCS_LOCAL_REPO_PATH (opt)   : The source location where the APF documentation page is checked out on local disk.
- CONFIG_LOCAL_REPO_PATH (opt) : The source location where the APF sample config is checked out on local disk.
- EXAMPLE_LOCAL_REPO_PATH (opt): The source location where the APF examples are checked out on local disk.
- BUILD_PATH                   : The path where the build is created and stored.

In case the configuration defines "DOCS_LOCAL_REPO_PATH" and/or "CODE_LOCAL_REPO_PATH" and/or "CONFIG_LOCAL_REPO_PATH" and/or "EXAMPLE_LOCAL_REPO_PATH" the build script tries to use a local clone of the APF repo(s).

The list of modules (-m) can either be "all" (default) or a comma-separated list of the following items (or just one):

- apidocs   : APF doxygen documentation.
- codepack  : Code release files.
- configpack: Sample configuration files.
- demopack  : Sandbox release files.
- examples  : Implementation examples (vbc, modules, calc).

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 12:32:04
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

### Create a build

In order to create a release build, you may want to use the following
call:

``` bash
$ for LOCAL_REPO in $(cat apf_build.conf | grep "_LOCAL_REPO_PATH" | cut -d "=" -f2); do cd $LOCAL_REPO && git pull origin; done
Already up-to-date.
Already up-to-date.
Already up-to-date.
Already up-to-date.

$ ./create_build.sh -c ../../../../Build/apf_build.conf -b release-2.0 -v 2.0.1 -m configpack
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 13:16:26
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Loading configuration file ../../../../Build/apf_build.conf
[INFO] Set global parameters ...
[INFO] Current build version: 2.0.1-2014-05-02-1316
[INFO] Exporting code branch release-2.0 to workspace ...
[INFO] create configpack release
[INFO] clean up temporary directories

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 13:16:29
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

In case you are using a local GIT repository it might be necessary to
make branches available locally. This can be achieved with

``` bash
for branch in $(git branch --list -a | grep -E "release|feature" | tr -d " " | cut -d "/" -f 3); do git branch -t $branch origin/$branch; done
```

for each of the configured repositories.

A complete build will be created with the following command:

``` bash
Christian@chrislap /cygdrive/c/Users/Christian/Entwicklung/GIT/tools/build/release
$ ./create_build.sh -c ../../../../Build/apf_build.conf -b release-2.0 -v 2.0.1 -m all
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Generate APF release                                                      ::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
start at: 2014-05-02, 13:39:14
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[INFO] Loading configuration file ../../../../Build/apf_build.conf
[INFO] Set global parameters ...
[INFO] Current build version: 2.0.1-2014-05-02-1339
[INFO] Exporting code branch release-2.0 to workspace ...
[INFO] generate documentation
[INFO] copy html documentation to current release folder
[INFO] create zip documentation files
[INFO] build code bases for php5 release
[INFO] create codepack release for php5
[INFO] create configpack release
[INFO] generate demopack basis for php5
[INFO] create demopack release packages for php5
[INFO] generate vbc example basis for php5
[INFO] create vbc example release packages for php5
[INFO] generate calc example basis for php5
[INFO] create calc example release packages for php5
[INFO] generate modules example basis for php5
[INFO] create modules example release packages for php5
[INFO] clean up temporary directories

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
end at  : 2014-05-02, 13:46:41
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
```

<languages />