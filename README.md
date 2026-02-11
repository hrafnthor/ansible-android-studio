# Ansible Android Studio

This role can download and configure multiple Android Studio clients for use on a Linux machine.

## Requirements

This role requires two separate tools be installed.

First it requires the `ansible.utils` collection be installed from Ansible-Galaxy via:

```bash
ansible-galaxy collection install ansible.utils
```

Secondly it requires the `jsonschema` Python package be installed via:

```bash
pip install jsonschema
```

## Role Variables

All parameters are optional unless otherwise stated.

```yaml

android_studio:
  clients:                      A list of version definitions to install or remove.
    - version:  [string]            [required] The version number for the Android Studio client to install.
      codename: [string]            If the remote artifact is named using a different codename than just the version, i.e 'android-studio-panda2-canary1-linux.tar.gz' instead of 'android-studio-2025.3.1.5-linux.tar.gz' then the codename should be set to 'panda2-canary1'. Otherwise the version will be used. See Google's own archive page for details.
      checksum: [string]            [required] The checksum for the version number archive being installed. See information below.
      remove:   [bool]              If true, no archive will be downloaded and any client matching the 'version' value will be removed.
  primary:                      The client that should be configured to be primary.
    version:    [string]            [required] The client version that should be marked as primary.
    symlink:                        If defined, will configure a symlink to the primary studio instance globally.
        name:   [string]                The name to give to the symlink, otherwise 'astudio' is used.
        path:   [string]                The path to where the symlink should be created, else `hth_android_studio_default_symlink_path`
        remove: [bool]                  If true, removes the symlink with the given name.
    desktop:                        If defined, will configure a desktop entry for the primary studio instance globally.
      name:     [string]                The name to give the desktop entry, otherwise 'Android Studio' is used.
      remove:   [bool]                  If true, removes the desktop entry for the primary studio client.
      keywords: [string array]          A list of strings that will be set as keywords in the desktop entry.
  canary:                       The client that should be configured to be the canary client.
    version:    [string]            [required] The client version that should be marked as primary.
    symlink:                        If defined, will configure a symlink to the primary studio instance globally.
        name:   [string]                The name to give to the symlink, otherwise 'astudioc' is used.
        path:   [string]                The path to where the symlink should be created, else `hth_android_studio_default_symlink_path`
        remove: [bool]                  If true, removes the symlink with the given name.
    desktop:                        If defined, will configure a desktop entry for the primary studio instance globally.
      name:     [string]                The name to give the desktop entry, otherwise 'Android Studio Canary' is used.
      remove:   [bool]                  If true, removes the desktop entry for the primary studio client.
      keywords: [string array]          A list of strings that will be set as keywords in the desktop entry.
  location:                     Allows for configuration of installation location.
    path:       [string]            Defines the installation path for new studio clients.
    owner:      [string]            Defines the owner of the installation path, otherwise 'root'.
    group:      [string]            Defines the group owning the installation path, otherwise the same as 'owner'.
    mode:       [string]            Defines the mode for the installation path, otherwise '0755'
  environment:                  Allows for configuration of environment variables.
    remove:     [bool]              If true, removes the file containing the environment variables.
    location:                       Allows for configuration of the environment file location.
      path:     [string]                Defines the path to where the environment file is located.
      owner:    [string]                Defines the owner of the environment file.
      group:    [string]                Defines the groupowning the environment file, otherwise the same as 'owner'.
    jdk_path:                   Allows for configuration of 'STUDIO_JDK' variable
      value:    [string]            [required] The value to set
      remove:   [bool]              If true, will remove the variable.
    sdk_path:                   Allows for configuration of 'ANDROID_HOME' variable.
      value:    [string]            [required] The value to set
      remove:   [bool]              If true, will remove the variable.
    preferences_path:           Allows for configuration of 'ANDROID_USER_HOME' variable.
      value:    [string]            [required] The value to set
      remove:   [bool]              If true, will remove the variable.
    emulator_path:              Allows for configuration of 'ANDROID_EMULATOR_HOME' variable.
      value:    [string]            [required] The value to set
      remove:   [bool]              If true, will remove the variable.
    avd_path:                   Allows for configuration of 'ANDROID_AVD_HOME' variable.
      value:    [string]            [required] The value to set
      remove:   [bool]              If true, will remove the variable.

```

For version numbers and their associated checksums seem the archives [here](https://developer.android.com/studio/archive).

### Defaults

By default the clients will be installed under `/opt/android/studio` as defined by the default variable `hth_android_studio_default_path`, unless the variable `android_studio.location.path` is given.

By default the location of the environment variables file is `/etc/profile.d/android.sh` as defined by the default variable `hth_android_studio_default_environment_path`, unless the variable `hth_android_studio.environment.location.path` is given.

By default all directories created will be owned by the user running the role, unless configured differently using variables such as `android_studio.location.owner | .group` or `android_studio.environment.location.owner | .group`. They are also given chmod `0755` by default.

By default the desktop file is placed at `/usr/share/applications` as defined by the default variable `hth_android_studio_default_desktop_file_path`.

By default the symlinks are placed in `/usr/bin` as defined by the default variable `hth_android_studio_default_symlink_path`, unless the variable `android_studio.(primary | canary).symlink.path` is given.


## Dependencies

None.

## Setup

Before the role can be used it needs to be added to the machine running the playbook, and as of writing this, this role is not hosted on Ansible-Galaxy only on Github.

1. Create a `requirements.yml` file in the root directory of the playbook being worked on.

2. Add the following definition inside the `requirements.yml` file:

```yml
- name: hth-android-studio
  src: https://github.com/hrafnthor/ansible-android-studio.git
  scm: git
```

3. Install the requirements by executing

```shell
ansible-galaxy install -r .requirements.yml
```

This will allow any playbook run from this machine to use the role `hth-android-studio`


## Example Playbook

The following playbook installs two clients. Each is installed to the default location, but with guid `developers`. One client is configured as the primary studio client, while the other one is set as the canary client. Each are given custom descriptive names and keywords. The canary client is symlinked only for user `Hrafn`. The environment file is create only for user `Hrafn`, with one incorrect value being removed.


```yaml
- hosts: all
    vars:
      android_studio:
        clients:
          - version: "2025.1.1.12"
            checksum: "9aa40cdfd3de3c5616220ba4ccddd7e2b4c73d471dac54dcfce0bdc38b7ec093"
          - version: "2025.1.2.6"
            checksum: "04e529e084b3e5001d94420b1233967b2526bbf54755f82b4be02c08ca359fbd"
        primary:
          version: "2025.1.1.12"
          symlink:
            name: "astudio"
          desktop:
            name: "Primary"
            keywords:
              - "android"
              - "studio"
              - "primary"
        canary:
          version: "2025.1.2.6"
          symlink:
            name: "astudioc"
            path: "/home/hrafn/.local/bin"
          desktop:
            name: "Canary"
            keywords:
              - "android"
              - "studio"
              - "canary"
        location:
          group: "developers"
          mode: "2775"
        environment:
          location:
            path: "/home/hrafn/.env.d"
            owner: "hrafn"
            group: "hrafn"
          jdk_path:
            value: "/tmp/not-correct-path/bin"
            remove: true
          sdk_path:
            value: "/usr/lib/android/sdk"
          preferences_path:
            value: "$HOME/.android"
          emulator_path:
            value: "$HOME/.android/emulators"
          avd_path:
            value: "$HOME/.android/avd"
  roles:
     - hth-android-studio
```

## License

```
Copyright 2025 Hrafn Thorvaldsson

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
