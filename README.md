# Ansible Role: Radarr

This role installs the Radarr application on Debian and also installs the recommended configuration for Movies or Anime, following the TRaSH guide for Radarr.

After running the role, the `radarr` service will be available on the server. The application's web interface can be accessed at `http://{your-container-ip}:7878`.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
radarr_repository_path: "/opt/Radarr"
```

Sets the repository destination path; if modified, make sure that the system user `radarr` still has the rights to make modifications.

```yaml
radarr_version: "4.0.20.3014"
```

Set the desired version of the app; this will be used to get the desired Sonarr prebuilt release.

```yaml
radarr_systemd_template: radarr.service.j2
```

The template to use when generating the systemd service.

```yaml
radarr_auth_username: "admin"
radarr_auth_password: "password"
```

Radarr credentials to create on the first start. Both values must be set using Ansible Vault or another secret manager; the ones provided are for light or temporary use. A production Radarr instance must have at least a strong private password.

When set, the role writes them to `/etc/default/radarr` with root-only permissions. Radarr creates the user only if its database does not already contain one, so changing these values later does not update an existing user. The username is lowercased by Radarr.

Keep the password in an Ansible Vault-encrypted variable rather than plain text:

## Dependencies

None.

## Example Playbook

```yaml
- hosts: server
  vars_files:
    - vars/main.yml
  roles:
    -  radarr
```

*Inside `vars/main.yml`*:

```yaml
sonarr_version: "6.4.4.10685"
```

## License

## Author Information

This role was created in 2026 by [tonirp](https://github.com/tonirp26)
