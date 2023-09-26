# Ansible Templates
Some of the templates I've created to clean up my Ansible configs!


| Template                   | Functionality |
| -------------------------- | ------------- |
| [docker.yaml](#dockeryaml) | Copies files, stops & starts docker compose |
| [service.yaml](#serviceyaml) | Copies files, links .service file, stops service, reloads daemon, starts service |
| [setup-user-docker-ssh.yaml](#setup-user-docker-sshyaml) | Ensure user exists, add user to `docker` group, add ssh keys |
| [sudoers-allow-service-management.yaml](#sudoers-allow-service-managementyaml) | Allow specified user to manage specified service |


## How-to
Either ...
- Copy the contents of one (or more) of the yaml files
- Use `git clone https://github.com/Denperidge/ansible-templates.git templates` to get all templates

### [docker.yaml](docker.yaml)
Example usage:
```yaml
- name: Server setups
  hosts: servers

  tasks:
    - include_tasks: templates/docker.yaml
      vars:
        name: "Plausible"
        src: "./plausible-files/"
        dest: "/home/user/plausible"

```

Note: [click here to view the explanation as to why no docker compose module is used](#no-docker-compose-module-usage)

### [service.yaml](service.yaml)
```yaml
- name: Server setups
  hosts: servers

  tasks:
    - include_tasks: templates/service.yaml
      vars:
        name: "caddy"
        src: "./caddy/"
        dest: "/home/user/caddy"
        servicefile: "/home/user/caddy/caddy.service"

```

### [setup-user-docker-ssh.yaml](setup-user-docker-ssh.yaml)
```yaml
- name: Server elevated setups
  hosts: servers
  become_user: root
  tasks:
    - include_tasks: templates/setup-user-docker-ssh.yaml
      loop:
        - { 
          username: "root", 
          sshkeys: 
            [ 
              {comment: "Desktop pc", file: "keys/id_rsa_desktop.pub"}, 
              {comment: "Laptop", file: "keys/id_rsa_laptop.pub"}, 
            ] 
          }
        - { 
          username: "user",
          sshkeys: 
            [ 
              {comment: "Desktop pc", file: "keys/id_rsa_desktop.pub"} 
            ] 
          }
```

### [sudoers-allow-service-management.yaml](sudoers-allow-service-management.yaml)
```yaml
- name: Server elevated setups
  hosts: servers
  become_user: root
  tasks:
    - include_tasks: templates/sudoers-allow-service-management.yaml
      vars:
        user: user
        servicename: caddy
        servicefile: "/home/user/caddy/caddy.service"
```

## Discussions
### No docker compose module usage


## License
Ansible is licensed under GNU GPLv3. You can view the [full license on the Ansible GitHub repository](https://github.com/ansible/ansible/blob/devel/COPYING).
These templates are released into the public domain through the Unlicense. You can [view the LICENSE file in this repository here](LICENSE).
