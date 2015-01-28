#  Ansible Server Provision Scripts

This repo contains my [Ansible](http://www.ansible.com/) provisioning scripts for my own servers. Currently this repo only contains a `bootstrap.yml` playbook, which is used for initially securing a freshly installed server, and a `site.yml` playbook for managing all my pre-bootstraped servers.

To bootstrap a servers run (replacing `$hostName` with the new server):
```
ansible-playbook bootstrap.yml -k -i production --vault-password-file ~/.vault_pass.txt -l $hostName
```

To manage all the servers run:
```
ansible-playbook site.yml -i production --ask-su-pass --vault-password-file ~/.vault_pass.txt
```

## Current Roles

#### Common
The common role configures creates the users, setting their shells to [my fork of oh-my-zsh](https://github.com/DomBlack/oh-my-zsh), sets their authorized SSH keys and adds them to the `ssh-users` user group.

It changes the SSH port, and sets some basic security such as only allowing users in the `ssh-users` group to login and disabling password based authentication.

#### Docker Host
The docker host role installs and configures docker as a publicly available service. The service is secured using client/server TLS certificates signed by a dedicate CA. The certifcates where generated as described [on dockers help pages on HTTPS](https://docs.docker.com/articles/https/).

By setting my `DOCKER_HOST` environmental variable on my development machine all local docker commands now execute on the remote server. `DOCKER_TLS_VERIFY=1` should also be set to ensure the client verifies the server's certificate.

## If forking

You will need to create a `production` file in the root, with contents like:
```
[general-hosts]
serverA.some-place.com

[docker-hosts]
serverB.some-host.com
```

You will also need to create your own `group_vars/all.yml` file, I have left an example in the group_vars folder to be used as a base
