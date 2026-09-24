# Ansible Collection - plopoyop.warpgate

Ansible collection to install and configure [Warpgate](https://github.com/warp-tech/warpgate), an open-source bastion host and smart proxy for SSH, HTTP, MySQL, PostgreSQL, and Kubernetes.

## Requirements

- Ansible >= 2.19.0
- Target platforms: Debian (trixie, bookworm), RHEL/EL (9, 10)
- `community.docker` (>=3.0.0, installed automatically as a collection dependency) and a working Docker Engine on the target host — only when using `warpgate_install_method: docker` (see below)

## Installation

```bash
ansible-galaxy collection install plopoyop.warpgate
```

Or add to your `requirements.yml`:

```yaml
collections:
  - name: plopoyop.warpgate
    version: ">=1.0.0"
```

## Roles

### `plopoyop.warpgate.install`

Installs and runs Warpgate, either from the official release binary (managed via systemd) or as the official Docker image (managed via the Docker Engine), configures the listeners, and generates the initial configuration file. Also supports backing Warpgate's own database with SQLite (default) or PostgreSQL.

```yaml
- hosts: bastions
  roles:
    - role: plopoyop.warpgate.install
      vars:
        warpgate_version: "0.27.1"
        warpgate_admin_password: "{{ vault_warpgate_admin_password }}"
        warpgate_http_port: 8888
        warpgate_ssh_enabled: true
        warpgate_ssh_port: 2222
        warpgate_external_host: "bastion.example.com"
```

Key variables:

| Variable | Default | Description |
|---|---|---|
| `warpgate_install_method` | `binary` | `binary` (systemd) or `docker` (Docker Engine) |
| `warpgate_version` | `0.27.1` | Warpgate release version / Docker image tag |
| `warpgate_admin_password` | *(required)* | Admin password |
| `warpgate_http_port` | `8888` | HTTPS listen port |
| `warpgate_ssh_enabled` | `false` | Enable SSH proxy |
| `warpgate_ssh_port` | `2222` | SSH listen port |
| `warpgate_mysql_enabled` | `false` | Enable MySQL proxy |
| `warpgate_postgres_enabled` | `false` | Enable PostgreSQL proxy (listener to PostgreSQL *targets*, unrelated to Warpgate's own database) |
| `warpgate_kubernetes_enabled` | `false` | Enable Kubernetes proxy |
| `warpgate_database_backend` | `sqlite` | Warpgate's own storage engine: `sqlite` or `postgres` |
| `warpgate_database_url` | *(auto)* | Database URL, auto-built from `warpgate_database_backend` (and `warpgate_database_postgres_*` when relevant); set directly to override |
| `warpgate_external_host` | `localhost` | External hostname for TLS |
| `warpgate_record_sessions` | `true` | Record session recordings |
| `warpgate_sso_providers` | `[]` | SSO provider configuration |

See the [install role documentation](roles/install/README.md) and [roles/install/defaults/main.yml](roles/install/defaults/main.yml) for the full list.

#### Running Warpgate via Docker

Set `warpgate_install_method: docker` to run the [official Warpgate image](https://github.com/warp-tech/warpgate/pkgs/container/warpgate) via the Docker Engine instead of installing the standalone binary. The role pulls the image, runs the one-shot `unattended-setup` in a throwaway container on first run, writes the generated config file to the host, and manages a long-running container with a Docker restart policy (no systemd unit is created).

Requirements: the `community.docker` collection (`>=3.0.0`, pulled in as a collection dependency) and a working Docker Engine already installed on the target host — this role does **not** install Docker itself.

```yaml
- hosts: bastions
  roles:
    - role: plopoyop.warpgate.install
      vars:
        warpgate_install_method: "docker"
        warpgate_admin_password: "{{ vault_warpgate_admin_password }}"
        warpgate_docker_host_data_path: "/var/lib/warpgate"   # bind-mounted to the container's /data
        warpgate_ssh_enabled: true
        warpgate_ssh_port: 2222
        warpgate_external_host: "bastion.example.com"
```

Docker-specific variables such as `warpgate_docker_image`, `warpgate_docker_image_tag`, `warpgate_docker_container_name`, `warpgate_docker_restart_policy`, `warpgate_docker_network_mode`, `warpgate_docker_publish_ports`, `warpgate_docker_extra_ports`, `warpgate_docker_extra_volumes` and `warpgate_docker_extra_env` let you customize the container without touching the role's tasks. See [roles/install/README.md](roles/install/README.md) for the full list. `warpgate_service_state` / `warpgate_service_enabled` still control whether the container is started/stopped and whether it comes back after a reboot (mapped to the Docker restart policy), for both install methods.

#### Using PostgreSQL as Warpgate's own database

By default Warpgate stores its own state (users, roles, targets, sessions, ...) in a local SQLite file. To use PostgreSQL instead:

```yaml
- hosts: bastions
  roles:
    - role: plopoyop.warpgate.install
      vars:
        warpgate_admin_password: "{{ vault_warpgate_admin_password }}"
        warpgate_database_backend: "postgres"
        warpgate_database_postgres_host: "db.example.com"
        warpgate_database_postgres_port: 5432
        warpgate_database_postgres_database: "warpgate"
        warpgate_database_postgres_username: "warpgate"
        warpgate_database_postgres_password: "{{ vault_warpgate_db_password }}"
        warpgate_database_postgres_sslmode: "require"
```

This is unrelated to `warpgate_postgres_enabled`, which toggles Warpgate's PostgreSQL-protocol *listener* used to proxy connections to PostgreSQL *targets*. Both can be used independently or together — Warpgate can, for instance, store its own state in PostgreSQL while also proxying to other PostgreSQL servers. Works with either `warpgate_install_method`. This role does not provision the PostgreSQL server itself — point it at an existing instance.

### `plopoyop.warpgate.configure`

Manages Warpgate resources (global parameters, roles, users, target groups, targets) via the admin API. Supports both API token and username/password authentication.

```yaml
- hosts: bastions
  roles:
    - role: plopoyop.warpgate.configure
      vars:
        warpgate_api_host: "https://bastion.example.com:8888/@warpgate/admin/api/"
        warpgate_admin_password: "{{ vault_warpgate_admin_password }}"

        warpgate_roles:
          - name: developers
            description: "Development team"
          - name: ops
            description: "Operations team"

        warpgate_users:
          - name: alice
            description: "Alice - Lead Developer"
            roles: [developers]
            credential_policy:
              ssh: [PublicKey]
              http: [Password, Totp]
            public_key_credentials:
              - label: "Work Laptop"
                public_key: "ssh-ed25519 AAAA..."

        warpgate_target_groups:
          - name: production
            description: "Production servers"
            color: Danger
          - name: staging
            description: "Staging servers"
            color: Warning

        warpgate_targets:
          - name: prod-web-01
            description: "Production web server"
            group: production
            roles: [developers, ops]
            ssh_options:
              host: 10.0.1.10
              port: 22
              username: deploy
              password_auth:
                password: "{{ vault_ssh_password }}"
```

Key variables:

| Variable | Default | Description |
|---|---|---|
| `warpgate_api_host` | *(required)* | Admin API URL |
| `warpgate_admin_username` | `admin` | Admin username |
| `warpgate_admin_password` | *(required)* | Admin password |
| `warpgate_api_token` | *(optional)* | API token (skips login if set) |
| `warpgate_api_insecure` | `false` | Disable TLS verification |
| `warpgate_parameters` | `{}` | Global parameters to enforce (Warpgate >= 0.24) |
| `warpgate_roles` | `[]` | Roles to manage |
| `warpgate_users` | `[]` | Users to manage |
| `warpgate_target_groups` | `[]` | Target groups to manage |
| `warpgate_targets` | `[]` | Targets to manage (SSH, HTTP, MySQL, PostgreSQL, Kubernetes) |

See the [configure role documentation](roles/configure/README.md) for details.

## Modules

All modules support `check_mode` and `diff` mode (`--diff`).

| Module | Description |
|---|---|
| `plopoyop.warpgate.warpgate_parameters` | Manage global parameters (singleton, Warpgate >= 0.24) |
| `plopoyop.warpgate.warpgate_role` | Manage Warpgate roles (incl. `is_default` auto-assignment, Warpgate >= 0.24) |
| `plopoyop.warpgate.warpgate_user` | Manage users, credential policies, passwords, SSH keys, and role assignments |
| `plopoyop.warpgate.warpgate_group` | Manage target groups |
| `plopoyop.warpgate.warpgate_target` | Manage targets (SSH, HTTP, MySQL, PostgreSQL, Kubernetes) with role assignments |
| `plopoyop.warpgate.warpgate_user_role` | Manage individual user-role associations |
| `plopoyop.warpgate.warpgate_password_credential` | Manage password credentials |
| `plopoyop.warpgate.warpgate_public_key_credential` | Manage SSH public key credentials |
| `plopoyop.warpgate.warpgate_ticket` | Manage temporary access tickets |

### Module usage example

```yaml
- name: Create a role
  plopoyop.warpgate.warpgate_role:
    host: "https://bastion.example.com:8888/@warpgate/admin/api/"
    token: "{{ warpgate_api_token }}"
    name: developers
    description: "Development team"

- name: Create an SSH target
  plopoyop.warpgate.warpgate_target:
    host: "https://bastion.example.com:8888/@warpgate/admin/api/"
    token: "{{ warpgate_api_token }}"
    name: prod-web-01
    group: production
    roles: [developers]
    ssh_options:
      host: 10.0.1.10
      port: 22
      username: deploy
      password_auth:
        password: "{{ vault_ssh_password }}"

- name: Create a temporary access ticket
  plopoyop.warpgate.warpgate_ticket:
    host: "https://bastion.example.com:8888/@warpgate/admin/api/"
    token: "{{ warpgate_api_token }}"
    username: alice
    target_name: prod-web-01
    expiry: "2026-12-31T23:59:59Z"
    number_of_uses: 1
  register: ticket

- name: Show ticket secret
  ansible.builtin.debug:
    msg: "Ticket: {{ ticket.secret }}"
```

With ansible-core >= 2.12 it is possible to specify default parameters for all modules in this collection using [Module defaults groups](https://docs.ansible.com/ansible/latest/user_guide/playbooks_module_defaults.html#module-defaults-groups). Use it like this:

```yaml
- hosts: localhost

  module_defaults:
    group/plopoyop.warpgate.warpgate:
      host: "https://bastion.example.com:8888/@warpgate/admin/api/"
      api_username: admin
      api_password: "{{ warpgate_admin_password }}"

  tasks:
    - name: Create a role
      plopoyop.warpgate.warpgate_role:
        name: developers
        description: "Development team"

    - name: Create an SSH target
      plopoyop.warpgate.warpgate_target:
        name: prod-web-01
        group: production
        roles: [developers]
        ssh_options:
          host: 10.0.1.10
          port: 22
          username: deploy
          password_auth:
            password: "{{ vault_ssh_password }}"
```

## Authentication

The modules and the `configure` role support two authentication methods:

1. **API token** (recommended): set `warpgate_api_token` or the `token` module parameter
2. **Username/password**: set `warpgate_admin_username` + `warpgate_admin_password` (the modules handle login and session management automatically)

When both are provided, the API token takes priority.

With username/password, each task opens an HTTP session on Warpgate and logs
out when it finishes, so no session is left behind. Token authentication does
not open any session at all — prefer it when the `configure` role manages a
large number of resources.

## Development

This project uses [devbox](https://www.jetify.com/devbox) for reproducible development environments:

```bash
devbox shell
task lint          # yamllint + ansible-lint + ruff
task test:modules  # pytest unit tests
task test:role -- install    # molecule test for install role
task test:role -- configure  # molecule test for configure role
```

## License

MPL-2.0
