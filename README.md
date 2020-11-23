# EDUCATIC-BBB-A13

Se trata de un sistema para instalar remotamente y de forma desatendida un sistema Big Blue Button complejo (con Greenlight, monitorización, backups, wiki, Rocketchat, etc...). El master del que deriva este fork utiliza Rocketchat, en nuestra instalación usamos Mattermost, así que tendremos que ponderar si mantenerlo o modificarlo. Este trabajo se basa en el realizado por el proyecto a13-ansible, del municipio alemán de Ulm. Se documenta en castellano, pero para compatibilidad internacional el código se mantendrá en inglés.

### Prepare

#### Install modules
```
git submodule update --init
ansible-galaxy install -r requirements.yml
```

Note: `cloudalchemy.node-exporter` requires the gnu variant of `tar` on macOS. (`brew install gnu-tar`)  
Note: `cloudalchemy.prometheus` requires the `jmespath` python module on your (deployer) machine

#### Passwords

You need to create the file `vault_password` and put the ansible vault password in there.

#### SSH Host Keys

Get all SSH host keys and store in local .ssh/known\_hosts file by executing following skript

```
ansible-playbook helper/store_known_hosts.yml
```

### Run
```
ansible-playbook main.yml
```

## HowTo
### Add new machine
* Update DNS zonefile in `files/coredns/zones/`
 * add A and AAAA records
 * update serial (`yyyymmddvv` with `vv` being the version increment. E.g., 2020040101)
* Update DNS by `ansible-playbook main.yml --tags dns`
* Enter Hostname twice in `inventory`, below `[all]` and below the other role the machine should have, eg. `[bbb]`
* Confirm that you can ssh into the machine by its newly aquired dns name (this also adds the host key to your `~/.ssh/known_hosts`)
* run `ansible-playbook main.yml -l your.fresh.hostname.example` (you may need `--user root` if you don't have an user yet, the base role creates one for you)
* ...?
* register your new bbb instance:
  * at the monitoring by running `ansible-playbook main.yml --tags monitoring`
  * at the loadbalancer by running `ansible-playbook main.yml --tags config`
* enable it manually in the loadbalancer

## Things to tell your Network Admin
* Proxy exeptions for IPv4 adress of turnserver 

