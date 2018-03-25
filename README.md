# Prometheus Server

Installs and configures a Prometheus server and relevant exporters on Debian Buster nodes.

## Justification + Architecture

There are many ways to manage the Prometheus installation. The approach taken previously is to install a set of
"exporters" - agents on a system, and expose those publicly - perhaps moderated by some sort of reverse proxy
that implements a basic authentication layer and TLS.

There are two problems with this the previous way of implementing Prometheus:

- The exporters are not packaged and the configuration to manage them is verbose.
- Many exporters must be exposed to the public, and the configuration there is also verbose.

In the case of multi-service hosts or clusters (desktop machines, some simple single-node service deployments or
Kubernetes clusters) it seems reasonable instead to use the federation feature of Prometheus to manage each set
of services as a single upstream configurable endpoint. This has a number of advantages:

- It's inherently more reliable to use federated servers as data is checkpointed at multiple points along the way
- When services change hands (for example, get passed between one service provider to another) monitoring is
  included with that package
- It allows a primitive set of ACLs, where the owners of a service can see relevant metrics but not the entire
  set of metrics across all services.

## Requirements

This is only possible on operating systems packing Prometheus 2+ (at the time of writing Debian "buster" or
"stretch-backports").

## Role Variables

The variables are all documented in `vars/main.yml`.

## Dependencies

There are no dependencies for this work.

## Installation

### Ansible Galaxy (recommended)

```bash
$ cd path/to/playbook/root
$ cat >> requirements.yaml <<EOF
- src: "https://github.com/littlemanco/ansible-role-prometheus"
  version: "master" # <----- Update this to a stable version
  name: "littlemanco.prometheus"
EOF
$ ansible-galaxy install -r requirements.yaml
```

### Git Submodules

```
$ cd path/to/playbook/root
$ mkdir roles/
$ git submodule add https://github.com/littlemanco/ansible-role-prometheus.git  roles/prometheus.prometheus
```

## Usage

Include this in another ansible playbook. For sample, consider a generic server playbook:

```
---
# $PLAYBOOK_ROOT/server.yaml
- name: "server"
  hosts: all
  become: true
  become_user: "root"
```

Add the reference for the role:

```
# $PLAYBOOK_ROOT/server.yaml
# ...
become_user: "root"
roles
  - "littlemanco.prometheus"
```

This should work!

## License

MIT

## Author Information

- https://www.andrewhowden.com/
