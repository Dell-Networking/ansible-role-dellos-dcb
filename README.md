DCB Role for Dell EMC Networking OS
======================================

This role facilitates the configuration of Data Center Bridging (DCB). It supports the configuration of DCB map and DCB buffer and assigns them to interfaces. This role is abstracted for dellos9.


Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-dcb
```

Requirements
------------
This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in OS connection variables or the ``provider`` dictionary.

Role Variables
--------------

This role is abstracted using the ``ansible_net_os_name`` variable  that can take the value "dellos9".

Any role variable with a corresponding state variable set to absent negates the configuration of that variable. For variables with no state variable, setting an empty value for the variable negates the corresponding configuration. The variables and its values are case-sensitive.

``dellos_dcb`` contains the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dcb_enable | boolean: true, false | Enables/disables DCB. |
| dcb_map | list | Configures the DCB map. See the following dcb_map.* keys for each list item. |
| dcb_map.name | string (required)         | Configures the DCB map name.                                     |
|dcb_map.priority_group | list           | Configures the priority group for DCB map. See the following priority_group.* keys for each list item.                                                                                        |
| priority_group.pgid | integer (required) choices: 0-7        | Configures the priority group ID.                     |
| priority_group.bandwidth | integer (required)         | Configures bandwidth percentage for the priority group.         |
| priority_group.pfc | boolean: true, false (required)         | Configures PFC on/off for the priorities in the priority group.                                                                                                     |
| priority_group.state | string, choices: absent, present*   | If set to absent, deletes the priority group of the DCB map.                                                                                                         |
| dcb_map.priority_pgid |string (required)       | Configures priority to priority group mapping. The value is the pgid of priority groups separated by space. For example:  1 1 2 2 3 3 3 4                                                                                                                                                     |
|dcb_map.intf | list           | Configures the DCB map to the interface. See the following intf.* keys for each list item.                                                                                                         |
| intf.name | string (required)        | Configures the DCB map to the interface with this interface name.             |
| intf.state | string, choices: absent, present*   | If set to absent, deletes the DCB map from the interface.         |
| dcb_map.state | string, choices: absent, present*   | If set to absent, deletes the DCB map.               |
| dcb_buffer | list | Configures the DCB buffer profile. See the following dcb_buffer.* keys for each list item. |
| dcb_buffer.name | string (required)         | Configures the DCB buffer profile name.                             |
| dcb_buffer.description | string (required)         | Configures a description about the DCB buffer profile.    |
|dcb_buffer.priority_params | list           | Configures the priority flow control buffer parameters. See the following priority_params.* keys for each list item.                                                                     |
| priority_params.pgid | integer (required) choices: 0-7        | Specifies the priority group ID.                     |
| priority_params.buffer_size | int (required)         | Configures the ingress buffer size (in KB) of the DCB buffer profile.                                                                                                      |
| priority_params.pause | integer          | Configures the buffer limit (in KB) for pausing.                           |
| priority_params.resume | integer          | Configures buffer offset limit (in KB) for resume.                         |
| priority_params.state | string, choices: absent, present*   | If set to absent, deletes the priority flow parameters of the DCB buffer.                                                                                           |
|dcb_buffer.intf | list           | Configures the DCB buffer to the interface. See the following intf.* keys for each list item.                                                                                                         |
| intf.name | string (required)        | Configures the DCB buffer to the interface with this interface name.       |
| intf.state | string, choices: absent, present*   | If set to absent, deletes the DCB buffer from the interface.    |
| dcb_buffer.state | string, choices: absent, present*   | If set to absent, deletes the DCB buffer profile.          |


```
Note: Asterisk (*) denotes the default value if none is specified. 
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish communication with the nodes in your inventory. This information can exist in
the Ansible group_vars or host_vars directories, or in the playbook itself.



|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified ``transport`` variable. The value of this key is the destination address for the transport. |
|        port | no       |            | Specifies the port used to build the connection to the remote device. If the value of this key is unspecified, the value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify a value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If this key does not specify a value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. If not specified, the device attempts to execute all commands in non-privileged mode.|
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If ``authorize`` is set to no, then this key is not applicable. If this key does not specify a value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictionary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictionary. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```
Dependencies
------------

The dellos-dcb role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example Playbook
----------------

The following example uses the dellos-dcb role to completely configure DCB map and DCB buffer profiles and assigns it to interfaces. The example creates a ``hosts`` file with the switch details and corresponding variables. The hosts file should define the variable `` ansible_net_os_name `` with corresponding Dell EMC networking OS name. It writes a simple playbook that only references the dellos-dcb role.  

Sample ``hosts`` file:
 
    leaf1 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9)>

Sample ``host_vars/leaf1``:

    hostname: leaf1
    provider:
      host: "{{ hostname }}"
      username: xxxxx 
      password: xxxxx
      authorize: yes
      auth_pass: xxxxx 
      transport: cli
    dellos_dcb:
        dcb_map:
          - name:  test
            priority_pgid: 0 0 0 3 3 3 3 0 
            priority_group:
              - pgid: 0
                bandwidth: 20
                pfc: true
                state: present
              - pgid: 3
                bandwidth: 80
                pfc: true
                state: present
            intf:
              - name: fortyGigE 1/8
                state: present
              - name: fortyGigE 1/9
                state: present
        state: present
        dcb_buffer:
          - name: buffer
            description:
            priority_params:
              - pgid: 0
                buffer_size: 5550
                pause: 40
                resume: 40
                state: present
            intf:
              - name: fortyGigE 1/8
                state: present
        state: present
        
Simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-dcb

Then run with:

    ansible-playbook -i hosts leaf.yaml

License
-------

Copyright (c) 2017, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
