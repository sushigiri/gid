# gid

> Git ID Manager

`gid` is a Git identity manager that helps switch between multiple "profiles"
of Git configurations. `gid` is non-intrusive, injecting configuration options
on a per-command basis rather than editing local or global Git configurations.
Furthermore, `gid` is a cross-platform utility that only requires an available
`git` command in the environment as a dependency.

## Usage

`gid` itself is a wrapper around the `git` command and can be used as a drop-in
replacement. All options and arguments passed to `gid` will be forwarded to the
`git` command with the active Git profile's configuration settings prepended.

```console
gid commit -m "This is a commit"
```

An accompanying utility `gidc` is used to select and manage Git profiles.

> **Warning**
> Before `gid` or its accompanying utility `gidc` are used, a configuration
> file must be created

### Configuration

`gid` usage requires an existing configuration file in [TOML](https://toml.io)
syntax.

This file must be named `gid.toml`, and can be placed in one of three locations
(listed in order of priority):

1. Any path pointed to by an environment variable `GID_CONFIG`
2. In the same directory as the `gid` and `gidc` binaries
3. In the user's configuration directory (on Windows, this is
  `%USERPROFILE%/.config/gid/gid.toml`; elsewhere, it is
  `$HOME/.config/gid/gid.toml`)

<details>
<summary markdown="span"><i>An example `gid.toml`</i></summary>

```toml
active = "profile_name_1"  # The current active Git profile, can be set
                           # automatically with the `gidc set` command

# Not all TOML types are supported, only valid Git values are allowed:
# booleans, integers, colors (string color/attribute name, 0-255, or 24 bit
# RGB hex code as either a single value or in an array), and strings ("yes",
# "off", paths, etc...)
#
# Details found here: https://git-scm.com/docs/git-config

[profile_name_1]
user.name = "my_git_username"
user.email = "my_git_email@example.com"
user.signingkey = "my_git_signingkey"
commit.gpgsign = true
tag.gpgsign = true
pull.rebase = false
core.sshCommand = "ssh -i \"$HOME/.ssh/my_ssh_key\""

# Profiles can specify as few or many configuration options as desired
[whatever_other_name]
user.name = "my_git_username_2"
user.email = "i-only-want-to-change-these-settings@example.com"
core.sshCommand = "ssh -i \"$HOME/.ssh/id_rsa\""
```
</details>

### `gidc`

The `gidc` utility can be used to manage Git profiles in the `gid`
configuration file. All commands and options can be found in the `--help`
section of the `gidc` utility.

```console
gidc --help
```

<details>
<summary markdown="span"><h4>Select an active profile</h4></summary>

An active Git profile can be selected with the `set` command.

```console
gidc set <profile_name>
```
> **Warning**
> The provided profile name must be a valid name in the configuration file
</details>

<details>
<summary markdown="span"><h4>List all profiles</h4></summary>

All available profile names in the configuration file can be listed with the
`list` command. The currently active profile will be marked with a `*`.

```console
gidc list
```
</details>

<details>
<summary markdown="span"><h4>Export profile to Git configuration</h4></summary>

Any defined profile can be exported to either a repository-local or the global
Git configuration with the `export` command.

```console
gidc export [-g] [profile_name]
```

The `-g` flag indicates whether the profile should be exported to the global
Git configuration. If not set, `export` will work with the local Git
configuration by default.

A profile name can be provided to `export` to specify which profile should be
used. If not provided, the current active profile will be used by default.
</details>

<details>
<summary markdown="span"><h4>Import Git configuration to profile</h4></summary>

Local or global Git configurations can be imported to an existing or new
profile with the `import` command.

```console
gidc import [-g] [profile_name]
```

The `-g` flag indicates whether the global Git configuration should be
imported. If not set, `import` will work with the local Git configuration by
default.

A profile name can be provided to `import` to specify which profile should be
used. If not provided, the current active profile will be used by default. A
new profile name can be provided to create a new profile.
</details>
