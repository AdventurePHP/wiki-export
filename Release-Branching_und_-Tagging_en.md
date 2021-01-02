Creating a release branches and tags have to be created according to the
repository documentation (see [GIT
Repository](/GIT_Repository "wikilink")). The following chapters briefly
describe the process.

## Branching

At first, a new release branch has to be created based on the current
development tree (*master*). This new tree is used to maintain the
release starting with the first delivery (e.g. *2.1.0*).

Please execute the following for the **code**, **examples**, and
**config** repositories:

``` bash
git checkout -b release-2.1 master
git push origin release-2.1
```

## Tagging

Based on the respective release branch, tags have to be created for each
delivery.

Please execute the following for the **code**, **examples**, and
**config** repositories:

``` bash
git tag -a 2.1 -m "Final release state for 2.1." release-2.1
git push origin 2.1
```

<languages />