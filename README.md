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

Sets the desired version of the app; this will be used to get the desired Radarr prebuilt release.

```yaml
radarr_systemd_template: radarr.service.j2
```

The template to use when generating the systemd service.

```yaml
radarr_admin_username: "admin"
radarr_admin_password: "password"
```

Radarr credentials to create on the first start. Both values must be set using Ansible Vault or another secret manager; the ones provided are for light or temporary use. A production Radarr instance must have at least a strong private password.

When set, the role writes them to `/etc/default/radarr` with root-only permissions. Radarr creates the user only if its database does not already contain one, so changing these values later does not update an existing user. The username is lowercased by Radarr.

Keep the password in an Ansible Vault-encrypted variable rather than plain text.

```yaml
radarr_auth_config:
  authenticationMethod: "forms"
  authenticationRequired: "enabled"
  username: "{{ radarr_admin_username }}"
  password: "{{ radarr_admin_password }}"
  passwordConfirmation: "{{ radarr_admin_password }}"
```

Radarr block that establishes the login form and sets the previously defined user and password. If you want to redefine this block in your playbook, remember that only your variable will be used instead of this one, so variables that are not added will not be used.

```yaml
  radarr_anime_profile: false
```

This variable defines the profile to use (common movies or anime movies) in the configuration for file sizes, custom formats and quality profiles.

```yaml
  radarr_quality_sizes_tv:
  ....

  radarr_quality_sizes_anime:
  ...
```

The file sizes that will be used on each profile, if you prefer to use a custom one.

```yaml
radarr_anime_format: "{Movie CleanTitle} ...."
radarr_movie_format: "{Movie CleanTitle} ...."

```

The naming scheme used in each profile and selected depending on the `radarr_anime_profile` var. In the API, this section is called `standardMovieFormat`.

```yaml
radarr_naming_scheme:
  renameMovies: true
  standardMovieFormat: "{{ radarr_desired_movie_format }}"
  movieFolderFormat: "{Movie CleanTitle} ({Release Year}) {tmdb-{TmdbId}}"
```

The naming scheme for movies and its folder.

```yaml
radarr_media_management:
  enableMediaInfo: true #True by default
  downloadPropersAndRepacks: "doNotPrefer"
```

The var for advanced media management configuration; `enableMediaInfo` corresponds to Analyze video files in the UI, so it's important to ensure it is true.

```yaml
radarr_anime_custom_formats:
  .....

radarr_movie_custom_formats:
  ....
```

List of custom formats that will be used from `files/custom_formats`, each element of the list is a .json with the corresponding .json defined in TRaSH guides so it is parsed to adapt the file for Radarr.

```yaml
radarr_quality_profiles_common:
  - remux+web-1080p.json

radarr_quality_profiles_anime:
  - "[anime]remux-1080p.json"
```

List of quality profiles to install. Each entry is a JSON file stored in `files/quality_profiles/` that describes the desired profile using quality, group, custom-format and language names.

The order of the `items` list is the quality preference order: the first entry appears at the top of the Radarr quality list and is the most preferred. Qualities inside the same group are treated as equal. Qualities and groups not listed are appended below as disabled.

To add another profile, save its definition in `files/quality_profiles/` and add the filename to `radarr_quality_profiles_common` or `radarr_quality_profiles_anime`.

```yaml
radarr_common_language: "Original"
```

`radarr_common_language` sets the language for every profile in `radarr_quality_profiles_common`. It must match a language name returned by Radarr, for example `"Original"` or `"English"`. Anime profiles keep the `language` value defined in their JSON file.

> [!WARNING]
> The role keeps only the profiles defined in the selected list. Any other existing profile is deleted, including Radarr's default profiles and profiles created manually. Radarr refuses to delete a profile that is in use by a movie, import list or collection, so reassign those items before changing the selected profile set.

The role updates an existing profile when its settings differ from the file, creates it when it is missing, and removes profiles that are not part of the selected set.

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
radarr_version: "6.4.4.10685"
```

## License

## Author Information

This role was created in 2026 by [tonirp](https://github.com/tonirp26)
