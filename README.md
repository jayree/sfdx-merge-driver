<!-- [![npm](https://img.shields.io/jayree/v/sfdx-merge-driver.svg)](https://npm.im/sfdx-merge-driver) [![license](https://img.shields.io/jayree/l/sfdx-merge-driver.svg)](https://npm.im/sfdx-merge-driver) [![Travis](https://img.shields.io/travis/jayree/sfdx-merge-driver.svg)](https://travis-ci.org/jayree/sfdx-merge-driver) [![AppVeyor](https://ci.appveyor.com/api/projects/status/github/jayree/sfdx-merge-driver?svg=true)](https://ci.appveyor.com/project/jayree/sfdx-merge-driver) [![Coverage Status](https://coveralls.io/repos/github/jayree/sfdx-merge-driver/badge.svg?branch=latest)](https://coveralls.io/github/jayree/sfdx-merge-driver?branch=latest) -->

# sfdx-merge-driver(1) --  Salesforce Metadata Specific Git Merge Driver
This is a git merge driver specific for Salesforce.com Metadata.

Now supports:
* Profiles
* Permission Sets
* Custom Labels

The merge is done based on the nodes of the files, checking if any node changed in both the local copy and the branch we're trying to merge, and merging automatically whenever there is no conflict.

In the case of a node being modified in our local workspace and in the branch we try to merge both nodes are marked with a conflict that specifies from which change the node comes so it makes it easier to identify and resolve the conflict.

### Automatic Setup (recommended):

To start using it right away:

```
$ npx sfdx-merge-driver install --global
```

**Or** install it locally, per-project:
```
$ cd /path/to/git/repository
$ npx sfdx-merge-driver install
```

### Example

```
$ npx sfdx-merge-driver install
$ git merge my-conflicting-branch
Conflicts Found: 2
Conflicts Found: 0
CONFLICT (content): Merge conflict in force-app/main/default/profiles/Admin.profile-meta.xml
Auto-merging force-app/main/default/profiles/Standard.profile-meta.xml
Automatic merge failed; fix conflicts and then commit the result.
```

### Advanced

The following section is only for advanced configuration of the driver if you have specific needs.

#### Setup Options

`sfdx-merge-driver install` supports a couple of config options:

`--driver` - string to install as the driver in the git configuration

`--driver-name` - string to use as the merge driver name in your configuration

`--files` - list of files that will trigger this driver

#### Install as Dependency

To avoid regular `npx` installs, consider installing the driver:

`$ yarn install [-g|--save-dev] npm-merge-driver`

#### Manual Setup (advanced):

`sfdx-merge-driver` requires two git configurations to work: a git configuration to add the driver to git, which is by default your local `.git/config` file, and a `gitattributes(5)` configuration, which is by default your local `.git/info/attributes`.

If you **do not** want `sfdx-merge-driver` to install itself for you:

Copy the `sfdx_merge.groovy` script and the directory `conf/` into `.git/scripts/sfdx-merge/` (create this directory if it doesn't exist yet).

Then edit the `.git/config` file and add the following lines:
```
[merge "sfdx-merge-driver"]
    name = A custom merge driver for Salesforce profiles
    driver = groovy .git/scripts/sfdx-merge/sfdx_merge.groovy %O %A %B .git/scripts/sfdx-merge
    recursive = binary
```

This will configure the merge driver, and configure it to run the groovy script, the merges of the metadata.

Then add the following lines to `.gitattributes` or `.git/info/attributes` (create this file if it doesn't exist yet):
```
*.profile merge=sfdx-merge-driver
*.profile-meta.xml merge=sfdx-merge-driver
*.permissionset merge=sfdx-merge-driver
*.permissionset-meta.xml merge=sfdx-merge-driver
*.labels merge=sfdx-merge-driver
*.labels-meta.xml merge=sfdx-merge-driver
```

This will instruct git that all the Salesforce files should be merged using the new merge drivers.
You can also choose which metadata you want to use this driver to.

#### Configure the driver
The merge driver uses the configuration in the file `.git/scritps/sfdx-merge/conf/merge-<metadataType>-config.json` to know what nodes to merge and how to identify if the nodes are the same node and if they are equal between the branches.

If a node type is not configured there, the merge driver will choose the one in the current branch.

#### Uninstalling

To remove an installed merge driver, use `sfdx-merge-driver uninstall`:

```
$ npx sfdx-merge-driver uninstall [--global] [--driver-name=sfdx-merge-driver]
```

## AUTHOR
Based on code from [Amguerrero's](https://github.com/amguerrero) repository [sfdc_merge](https://github.com/amguerrero/sfdc_merge)

Installer based on code from [Zkat's](https://github.com/zkat) repository [npm-merge-driver](https://github.com/npm/npm-merge-driver)

## SEE ALSO

* `git-config(1)`
* `gitattributes(5)`
