hth-android-studio
=========

This role can download and configure multiple Android Studio clients for use on a Linux machine.

Requirements
------------

None.


Role Variables
--------------

All parameters are optional unless otherwise stated.

`android_studio_path` : [string] The absolute path to where the studio versions will be downloaded to (default: `/opt/android-studio`)

`android_studio_jdk` : [string] The absolute path to the Java version which should be specifically used with Android Studio via the $STUDIO_JDK environment variable (default: not set).

---

`android_studio_clients` : [mapping] Defines the Android Studio clients that will be downloaded.

###### Structure of `android_studio_clients` mapping

`version` : [string] (required) The Android Studio client version to download, for instance `2022.3.1.17`. See a full list [here](https://developer.android.com/studio/archive).

`checksum` : [string] (required) The checksum matching the requested Android Studio client version. Find the checksum matching the client [here](https://developer.android.com/studio/archive).

---

`android_studio_primary` : [mapping] Defines options for selecting and configuring which client version will be the primary Android Studio client.

###### Structure of `android_studio_primary` mapping

`version` : [string] (required) The version number of the client which should be configured to be the primary Android Studio client. This client version will have it's `studio.sh` symlinked to `/usr/bin/astudio`.

`symlink` : [string] Allows settings a custom name to use when symlinking the version launch script to `/usr/bin/`. Defaults to `astudio` for primary client.

`desktop` : [mapping] Contains configuration on desktop entry creation if requested.

    `create` : [boolean] (required) Indicates if the desktop entry should be created

    `name` : [string] Allows renaming the name used in the created desktop entry.

---

`android_studio_canary` : [mapping] Defines options for selecting and configuring which client version will be the canary Android Studio client.

###### Structure of `android_studio_canary` mapping

`version` : [string] (required) The version number of the client which should be configured to be the primary Android Studio client. This client version will have it's `studio.sh` symlinked to `/usr/bin/astudioc`.

`symlink` : [string] Allows settings a custom name to use when symlinking the version launch script to `/usr/bin/`. Defaults to `astudioc` for primary client.

`desktop` : [mapping] Contains configuration on desktop entry creation if requested.

    `create` : [boolean] (required) Indicates if the desktop entry should be created

    `name` : [string] Allows renaming the name used in the created desktop entry.


Dependencies
------------

None.

Setup
-----

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


Example Playbook
----------------

The following example playbook installs two different Android Studio clients and makes one the primary client and the other the canary client. The primary client has a desktop entry created, and with a different entry name then the default one. The canary version does not have a desktop entry created but overrides the name used for the symlinked launcher. The Java version to use for running Android Studio, no matter the client version, is set to OpenJDK 11.

```yaml
- hosts: all
    vars:
    - android_studio_path: "/opt/android-studio"
    - android_studio_jdk: "/usr/lib/jvm/java-11-openjdk-amd64"
    - android_studio_clients:
        - version: "2022.2.1.9"
          checksum: "7e7868b83bca8255f690e6c7fe026a3f3be1e140d2f09682c2b150af8cf93550"
        - version: "2021.3.1.17"
          checksum: "89adb0ce0ffa46b7894e7bfedb142b1f5d52c43c171e6a6cb9a95a49f77756ca"
    - android_studio_primary:
        - version: "2021.3.1.17"
          desktop: 
            create: yes
            name: "AStudio Stable"
    - android_studio_canary: 
        - version: "2022.2.1.9"
          symlink: "androidstudio"
  roles:
     - hth-android-studio
```

License
-------

MIT license. See attached license file.

Author Information
------------------

Hrafn Thorvaldsson
Find me at https://www.hth.is
