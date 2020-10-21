[![Build Status](https://travis-ci.org/OCA/maintainer-tools.svg?branch=master)](https://travis-ci.org/OCA/maintainer-tools)
[![Coverage Status](https://img.shields.io/coveralls/OCA/maintainer-tools.svg)](https://coveralls.io/r/OCA/maintainer-tools?branch=master)

# OCA Maintainers Tools

## Installation

    $ git clone git@github.com:OCA/maintainer-tools.git
    $ cd maintainer-tools
    $ virtualenv env
    $ . env/bin/activate
    $ python setup.py install
    
Alternatively if using pipx.
    
    $ git clone git@github.com:OCA/maintainer-tools.git
    $ pipx install ./maintainer-tools

## OCA repositories tools

These tools are mostly for maintenance purpose only.
They are used by OCA maintainers to address common operations across all repos.

**Prerequisite**

Get a token from Github.

    $ oca-github-login USERNAME


NOTE: you may have to delete the existing one from
"Account settings -> Developer Settings -> Personal Access Tokens".


### Sync team users from community.odoo.com to GitHub teams

Goal: members of the teams should never be added directly on GitHub.
They should be added on https://community.odoo.com. This script will
sync all the teams from Odoo to GitHub.

Prerequisites:

* Your odoo user must have read access to the projects and users;
* The partners on odoo must have their GitHub login set otherwise they won't
  be added in the GitHub teams;
* Your GitHub user must have owners rights on the OCA organization to be
  able to add or remove members;
* The odoo project must have the same name than the GitHub teams.

Run the script in "dry-run" mode:

    $ oca-copy-maintainers --dry-run

Apply the changes on GitHub:

    $ oca-copy-maintainers

The first time it runs, it will ask your odoo's username and password.
You may store them using the `--store` option, but watch out: the password is stored in clear text.


### Set labels on OCA repository on GitHub

Set standardized labels to ease the issue workflow on all repositories with same colors.
This tools will also warn you what are the specific labels on some repository

    $ oca-set-repo-labels


### Clone all OCA repositories

The script `oca-clone-everything` can be used to clone all the OCA projects:
create a fresh directory, use oca-github-login (or copy oca.cfg from a place
where you've already logged in) and run oca-clone-everything.

The script will create a clone for all the OCA projects registered on
github. For projects already cloned, it run `git fetch --all` to get the
latest versions.

If you pass the `--organization-remotes
<comma-separated-list>` option, the script will also add remotes for the listed
accounts, and run `git fetch` to get the source code from these forks. For instance:

    $ oca-clone-everything --organization-remotes yourlogin,otherlogin

will create two remotes, in addition to the default `origin`, called
`yourlogin` and `otherlogin`, respectively referencing
`git@github.com:yourlogin/projectname` and
`git@github.com:otherlogin/projectname` and fetch these remotes, for all the
OCA projects. It does not matter whether the forks exist on github or not, and
you can create them later.


## Quality tools

These tools are meant to be used both by repo maintainers and contributors.
You can leverage them to give more quality to your modules and to respect OCA guidelines.


### README generator

To provide high quality README for our modules we generate them automatically.
The sections of the final README are organized in fragments.
They must be put inside a `readme` folder respecting [this structure|./readme].

#### Run and relax

You should be able to generate `README.rst` files for all modules within a repository 
by simply typing inside that repo dir.

    $ oca-gen-addon-readme
    
#### Organization Name

The organization name is provided in [--org-name]. It is used to generate the final URL
to the repository. By default it takes `indexa-git`.

    $ oca-gen-addon-readme --org-name iterativo-git
    
#### Repository Name

The repository name is provided in [--repo-name]. It is used to generate the final URL
to the repository. By default it takes the name of the working addons directory.

    $ oca-gen-addon-readme --repo-name dominican-bank-statement
 
- If you execute the command inside a repository directory it will take that dir name as
repository name.

- If you execute the command inside a module directory it will take the parent dir name as
repository name.
    
#### Branch

The branch is provided in [--branch]. It is used to generate the final URL to the repository.
By default it takes the current branch of the addons directory. Meaning it will execute 
[git branch -C <addons dir> --show current] in the dir it is working on.

    $ oca-gen-addon-readme --branch 12.0
    
#### Addon Directory

The addon directory is provided in [--addon-dir]. Use this option if you only want to generate
`README.rst` for that module.

    $ oca-gen-addon-readme --addon-dir acap_bank_statement_import
    
Or if you are inside a module

    $ oca-gen-addon-readme --addon-dir .
    
You can also use absolute paths

    $ oca-gen-addon-readme --addon-dir /Users/cadara/PycharmProjects/indexa/dominican-bank-statement/acap_bank_statement_import

#### Addons Directory

The addons directory is provided in [--addons-dir]. Use this option if you want to generate
`README.rst`for all modules within a directory.

    $ oca-gen-addon-readme --addons-dir dominican-bank-statement

- If you are inside a repository directory it will take the current working directory by default.

- If you are inside a module directory it will take the parent directory by default.
    
You can also use absolute paths

    $ oca-gen-addon-readme --addons-dir /Users/cadara/PycharmProjects/indexa/dominican-bank-statement
    
#### Committing

The committing options are provided in [--commit/--no-commit]. Use [--commit] if you want all changes 
for each module to be automatically commited. Use [--no-commit] if you want all changes to be left unstaged.
By default it takes [--commit].

    $ oca-gen-addon-readme --commit
    
or

    $ oca-gen-addon-readme --no-commit
    
#### Minimal Documentation

The minimal documentation options are provided in [--minimal/--all-fragments]. Use [--minimal] if you
only want to generate a `README.rst` file with required fragments. Use [--all-fragments] if you want
to generate `README.rst`with all recommended fragments.

    $ oca-gen-addon-readme --all-fragments
    
or

    $ oca-gen-addon-readme --minimal

- Required fragments
    
    DESCRIPTION
    
- Recommended fragments

    CONFIGURE
    USAGE
    CONTRIBUTORS
    CREDITS
    
#### Extra Fragments

Extra fragments are provided in [--extra-fragments] option. Use this option if you
want to add additional fragments to the `README.rst` and automatically create the files.

    $ oca-gen-addon-readme --extra-fragments fragment1 extra_fragment_2
    
The fragment headers will be taken from the `FRAGMENT_EXAMPLE.rst` file. Replacing
Underscore `_` with blank space and styling as a title.

    - fragment1 --> Fragment1
    - extra_fragment_2 --> Extra Fragment 2
    
#### Verbose

Verbose is provided in [--verbose] option. Use this option if you want information of
the command execution to be prompted to shell. This also triggers the [--quiet] option
when committing. By default it's false.

    $ oca-gen-addon-readme --verbose

### Changelog generator using towncrier

To facilitate the generation of the changelog of addons, we have a
small wrapper around [towncrier](https://pypi.org/project/towncrier/).
For example, this will update HISTORY.rst for `mis_builder` and `mis_builder_budget`
with the version found in their manifest:

    $ oca-towncrier --repo=mis-builder --addon-dir=mis_builder --addon-dir=mis_builder_budget --commit


### Icon generator

To provide an icon for our modules we generate them automatically.

To generate the icon for the module `auth_keycloak`:

    $ oca-gen-addon-icon --addon-dir=auth_keycloak


### Auto fix pep8 guidelines

To auto fix pep8 guidelines of your code you can run:

    $ oca-autopep8 -ri PATH

This script overwrite with monkey patch the original script of [autopep8](https://github.com/hhatto/autopep8)
to support custom code refactoring.

* List of errors added:

    - `CW0001` Class name with snake_case style found, should use CamelCase.
    - `CW0002` Delete vim comment.

More info of original autopep8 [here](https://pypi.python.org/pypi/autopep8/)

You can rename snake_case to CamelCase with next command:

    $ oca-autopep8 -ri --select=CW0001 PATH

You can delete vim comment

    $ oca-autopep8 -ri --select=CW0002,W391 PATH


## Developers

As a developer, you want to launch the scripts without installing the
egg.

    $ git clone git@github.com:OCA/maintainer-tools.git
    $ cd maintainer-tools
    $ virtualenv env
    $ . env/bin/activate
    $ pip install -e .

**Run tests**

    $ tox  # all tests for all python versions
    $ tox -e py27  # python 2.7
    $ tox -- -k readme -v  # run tests containing 'readme' in their name, verbose

**Get a token from Github**

    $ python -m tools.github_login USERNAME

**Run a script**

    $ python -m tools.copy_maintainers

You can use the `GITHUB_TOKEN` environment variable to specify the token

    $ GITHUB_TOKEN=xxx python -m tools.copy_maintainers

## Integration with `pre-commit`

In any addons repo, you can use these pre-commit hooks:

```yaml
# .pre-commit-config.yaml file
repos:
  - repo: https://github.com/OCA/maintainer-tools
    rev: master # This is just an example; you must use a tag/commit instead!
    hooks:
      # Use each script's `--help` to understand the args
      - id: oca-gen-addon-readme
        args:
          - --addons-dir=.
          - --org-name=OCA
          - --repo-name=server-tools
          - --branch=13.0

      # This job could easily produce conflicts when it runs on every commit,
      # so it's added as a manual job. If you automate it, beware.
      # See https://pre-commit.com/#confining-hooks-to-run-at-certain-stages
      - id: oca-gen-addons-table
        stages: [manual]

      - id: oca-gen-addon-icon
        args:
          - --addons-dir=.
```
