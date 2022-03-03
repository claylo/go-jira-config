# go-jira-config

Shared configuration modifications for [go-jira](https://github.com/go-jira/jira)

## Getting Started

### Step 1. Check out this repo

Via SSH:

```
git clone git@github.com:claylo/go-jira-config.git ~/.jira.d
```

Or via HTTPS:

```
git clone https://github.com/claylo/go-jira-config.git ~/.jira.d
```

### Step 2. Setup

Install [go-jira](https://github.com/go-jira/jira). On macOS:

```
brew update && brew install go-jira
```

### Step 3. Confirm settings

From anywhere on your system, type `jira help`. If everything's working, custom commands in this repository will appear in the help output. Look for `mine` and `new-issue` near the bottom of the list.

You can see the generated configuration this repository facilitates by running:

```
jira conf
```

## Custom Setup

If you're like me, you work with multiple clients and therefore interact with multiple Jira installations all the time.

To do this, I keep a directory symlinked into my `$HOME` directory called, fantastically, `work`. Within `work` I have a directory per client, each with their own Jira configurations. That winds up looking like this:

```
work
├── acme
│   └── .jira.d
│       └── auth.yml
├── example
│   └── .jira.d
│       └── auth.yml
└── obvious
    └── .jira.d
        └── auth.yml

6 directories, 3 files
```

Each auth.yml file contains something like:

```yaml
endpoint: https://acme.atlassian.net
user: clay@php.net
password-source: keyring
```

### Keep it DRY

If all your `password-source` values are `keyring`, create a file in `~/jira.d/custom/dry.yml` and add:

```yaml
password-source: keyring
```

... to that, and then remove the `password-source` entries from each individual `auth.yml` file. Then you can check the assembly of dynamic configuration for jira with:

```
jira conf
```

Which will show something like this:

```yaml
%YAML 1.1
---
#
# An opinionated and modular executable config for go-yaml.
#
# Installation: Copy this file to ~/.jira.d/config.yml and make sure it's
# exectuable.
#
# NOTE: This loader will _not_ read from any paths ending in .jira.d/config.yml,
# because go-yaml picks those up recursively already.
#
# Key to the generated comments:
#
#   INC(auth) ........... First filename matching auth.yml.
#   INC(cc) ............. Custom Command(s) found in file.
#   INC(cmd) ............ Custom Command(s) found in filename matching cmd-*.yml.
#   INC(yml) ............ File found in a .jira.d directory with no
#                           custom commands.
#   SKIP(auth) .......... Skipped auth.yml file, because one was found closer
#                           to the current working directory.
#   SKIP(config) ........ Skipped config.yml file. go-jira will find and load.
#   SKIP(exec) .......... Skipped an executable .yml file.
#

# INC(auth):     ~/work/example/.jira.d/auth.yml
endpoint: https://example.atlassian.net
user: clay@example.com

# SKIP(config):  ~/.jira.d/config.yml
# INC(yml):      ~/.jira.d/custom/dry.yml
password-source: keyring

custom-commands:
  #
  # INC(cmd): ~/.jira.d/commands/cmd-env.yml
  #
  - name: env
    help: print the JIRA environment variables available to custom commands
    script: |
      env | grep JIRA
  #
  # INC(cmd): ~/.jira.d/commands/cmd-conf.yml
  #
  - name: conf
    help: output the executable ~/.jira.d/config.yml
    script: |
      ~/.jira.d/config.yml

...
```
