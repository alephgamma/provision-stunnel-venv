# provision-stunnel-venv

This is an ansible environment to provision a basic point-to-point STUNNEL connection between to end-points. In other terms, it is an TLS/SSL tunnel for services that do not natively support TLS/SSL. Another way to understand STUNNELs is that the client connects to the server on localhost port 2222 which is then encrypted and tunneled to the server over port 3129 and then dropped onto a process on the server listening on port 3128. They key concept is that STUNNELs operate at the TCP port level (layer 4) and conceptually are "port stitching" from a process on the client to the server.

## The Big Picture

For us that learn better by seeing, the image below is for visualization.
![alt text](https://github.com/alephgamma/provision-stunnel-venv/blob/main/provision-stunnel-venv.png?raw=true)

## The Eternal Problem

The inventory file needs the IP addresses to the target nodes. Based on the server provider, these will change. But the structure is basically:
```
[server]
proxy ansible_host={{ SERVER-IP }}

[client]
alpha ansible_host={{ CLIENT-IP }}
```

## Run the playbooks

Many assumptions satisfied (virtual environment enabled, authentication, keys distributed, sudo, etc) usually answered in the `ansible.cfg` file, the playbook runs:
```
[provision-stunnel-venv]$ ansible-playbook playbooks/provision-stunnel.yml
```

When all is well with the provisioning playbook, now run the verification playbook:
```
[provision-stunnel-venv]$ ansible-playbook playbooks/verification.yml

PLAY [validate proxy] ********************************************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [alpha]

TASK [get_uri] ***************************************************************************************
fatal: [alpha]: FAILED! => {"changed": false, "elapsed": 10, "msg": "Status code was -1 and not [200]: Request failed: <urlopen error [Errno 104] Connection reset by peer>", "redirected": false, "status": -1, "url": "https://www.google.com"}

PLAY RECAP *******************************************************************************************
alpha                      : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

But the playbook fails and this is because the server provider has a firewall (or there may be an internal firewall as well) which needs to be opened for port 3129.

![alt text](https://github.com/alephgamma/provision-stunnel-venv/blob/main/provision-stunnel-venv-blocked.png?raw=true)

## Open the server for port 3129

Each provider (or OS) is different. Depending on how this done, it may need to be manual until this function can be modularized.

## It Works
A quick verification, but this is not nor should be considered monitoring...
```
[provision-stunnel-venv]$ ansible-playbook playbooks/verification.yml

PLAY [validate proxy] ********************************************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [alpha]

TASK [get_uri] ***************************************************************************************
ok: [alpha]

PLAY RECAP *******************************************************************************************
alpha                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
