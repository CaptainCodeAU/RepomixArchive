This file is a merged representation of the entire codebase, combined into a single document by Repomix.

# File Summary

## Purpose
This file contains a packed representation of the entire repository's contents.
It is designed to be easily consumable by AI systems for analysis, code review,
or other automated processes.

## File Format
The content is organized as follows:
1. This summary section
2. Repository information
3. Directory structure
4. Multiple file entries, each consisting of:
  a. A header with the file path (## File: path/to/file)
  b. The full contents of the file in a code block

## Usage Guidelines
- This file should be treated as read-only. Any changes should be made to the
  original repository files, not this packed version.
- When processing this file, use the file path to distinguish
  between different files in the repository.
- Be aware that this file may contain sensitive information. Handle it with
  the same level of security as you would the original repository.

## Notes
- Some files may have been excluded based on .gitignore rules and Repomix's configuration
- Binary files are not included in this packed representation. Please refer to the Repository Structure section for a complete list of file paths, including binary files
- Files matching patterns in .gitignore are excluded
- Files matching default ignore patterns are excluded

## Additional Info

# Directory Structure
```
.github/
  workflows/
    reuse.yml
defaults/
  main.yml
docs/
  configuring-jitsi.md
LICENSES/
  AGPL-3.0-or-later.txt
  CC0-1.0.txt
meta/
  main.yml
tasks/
  util/
    prosody_post_setup_hooks/
      setup_jitsi_auth_internal.yml
    setup_jitsi_auth_uvs_install.yml
    setup_jitsi_auth_uvs_uninstall.yml
    setup_jitsi_prosody_post_setup_hooks.yml
  init_additional_jvb.yml
  jicofo_install.yml
  jicofo_uninstall.yml
  jvb_install.yml
  jvb_uninstall.yml
  main.yml
  prosody_install.yml
  prosody_uninstall.yml
  self_check_matrix_auth.yml
  setup_jitsi_base.yml
  validate_config.yml
  web_install.yml
  web_uninstall.yml
templates/
  jicofo/
    env.j2
    jicofo.service.j2
    logging.properties.j2
    sip-communicator.properties.j2
  jvb/
    custom-sip-communicator.properties.j2
    env.j2
    jvb.service.j2
    labels.j2
    logging.properties.j2
  prosody/
    env.j2
    labels.j2
    prosody.service.j2
  web/
    base.html.j2
    custom-config.js.j2
    custom-interface_config.js.j2
    env.j2
    labels.j2
    web.service.j2
justfile
LICENSE
README.md
REUSE.toml
```

# Files

## File: .github/workflows/reuse.yml
````yaml
# SPDX-FileCopyrightText: 2022 Free Software Foundation Europe e.V. <https://fsfe.org>
#
# SPDX-License-Identifier: CC0-1.0
---
name: REUSE Compliance Check

on: [push, pull_request]

permissions:
  contents: read

jobs:
  reuse-compliance-check:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: REUSE Compliance Check
        uses: fsfe/reuse-action@v5
````

## File: defaults/main.yml
````yaml
# SPDX-FileCopyrightText: 2023 - 2024 Slavi Pantaleev
# SPDX-FileCopyrightText: 2023 - 2024 Nikita Chernyi
# SPDX-FileCopyrightText: 2024 - 2025 adam-kress
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

# Project source code URL: https://github.com/jitsi/docker-jitsi-meet

jitsi_enabled: true

jitsi_identifier: jitsi

jitsi_base_path: "/{{ jitsi_identifier }}"

jitsi_version: stable-10078-1

# The hostname at which Jitsi is served.
jitsi_hostname: ''

# The path at which Jitsi is served.
# This value must either be `/` or not end with a slash (e.g. `/jitsi`).
jitsi_path_prefix: /

jitsi_uid: ''
jitsi_gid: ''

# Username corresponding to jitsi_uid. If undefined, it will be auto-detected.
jitsi_user_username: ''

jitsi_architecture: ''

jitsi_enable_auth: false
jitsi_enable_guests: false
jitsi_enable_recording: false
jitsi_enable_transcriptions: false
jitsi_enable_jaas_components: false
jitsi_enable_p2p: true
jitsi_enable_av_moderation: true
jitsi_enable_breakout_rooms: true
jitsi_disable_gravatar: false

# Authentication type, must be one of internal, jwt, matrix or ldap.
# Currently, only internal, matrix and ldap mechanisms are supported by this role.
# matrix auth verifies against matrix openID, and requires a user-verification-service to run.
jitsi_auth_type: internal

# A list of Jitsi (Prosody) accounts to create using the internal authentication mechanism.
#
# Accounts added here and subsequently removed will not be automatically removed
# from the Prosody server until user account cleaning is integrated into the role.
#
# Example:
# jitsi_prosody_auth_internal_accounts:
#  - username: "jitsi-moderator"
#    password: "secret-password"
#  - username: "another-user"
#    password: "another-password"
jitsi_prosody_auth_internal_accounts: []

# Configuration options for LDAP authentication. For details see upstream:
#   https://github.com/jitsi/docker-jitsi-meet#authentication-using-ldap.
# Defaults are taken from:
#   https://github.com/jitsi/docker-jitsi-meet/blob/master/prosody/rootfs/defaults/saslauthd.conf
jitsi_ldap_url: ""
jitsi_ldap_base: ""
jitsi_ldap_binddn: ""
jitsi_ldap_bindpw: ""
jitsi_ldap_filter: "uid=%u"
jitsi_ldap_auth_method: "bind"
jitsi_ldap_version: "3"
jitsi_ldap_use_tls: false
jitsi_ldap_tls_ciphers: ""
jitsi_ldap_tls_check_peer: false
jitsi_ldap_tls_cacert_file: "/etc/ssl/certs/ca-certificates.crt"
jitsi_ldap_tls_cacert_dir: "/etc/ssl/certs"
jitsi_ldap_start_tls: false

# Auth type: matrix
jitsi_prosody_auth_matrix_user_verification_repo_location: "https://github.com/matrix-org/prosody-mod-auth-matrix-user-verification"
jitsi_prosody_auth_matrix_user_verification_repo_target:
  - "{{ jitsi_prosody_ext_path }}/prosody_auth_matrix_user_verification"
jitsi_prosody_auth_matrix_user_verification_repo_version: "2839499cb03894d8cfc3e5b2219441427cb133d8"  # v1.8.0
jitsi_prosody_auth_matrix_uvs_sync_power_levels: false
jitsi_prosody_auth_matrix_uvs_location: ""
# Should match domain, see https://github.com/vector-im/element-web/pull/15114/commits/0410a6b3be82a41457275e4d1ce879dea146e092
jitsi_prosody_auth_matrix_jwt_app_id: "{{ jitsi_hostname }}"
jitsi_prosody_auth_matrix_files:
  - path: "mod_auth_matrix_user_verification.lua"
    when: true
  - path: "mod_matrix_power_sync.lua"
    when: "{{ jitsi_prosody_auth_matrix_uvs_sync_power_levels }}"

jitsi_prosody_auth_matrix_uvs_auth_token: ''

# Default max participants to the empty string
#
# The setting requires an integer to be set for usage and allows a user to specify the max number of particpants on a conference.
jitsi_prosody_max_participants: ''

jitsi_timezone: UTC

jitsi_xmpp_domain: meet.jitsi
jitsi_xmpp_server: xmpp.meet.jitsi
jitsi_xmpp_auth_domain: auth.meet.jitsi
jitsi_xmpp_bosh_url_base: http://{{ jitsi_xmpp_server }}:5280
jitsi_xmpp_guest_domain: guest.meet.jitsi
jitsi_xmpp_muc_domain: muc.meet.jitsi
jitsi_xmpp_internal_muc_domain: internal-muc.meet.jitsi
jitsi_xmpp_modules: ''

jitsi_recorder_domain: recorder.meet.jitsi

jitsi_jibri_brewery_muc: jibribrewery
jitsi_jibri_pending_timeout: 90
jitsi_jibri_xmpp_user: jibri
jitsi_jibri_xmpp_password: ''
jitsi_jibri_recorder_user: recorder
jitsi_jibri_recorder_password: ''

jitsi_enable_lobby: false

jitsi_container_image_tag: "{{ jitsi_version }}"

# The base container network. It will be auto-created by this role if it doesn't exist already.
jitsi_container_network: "{{ jitsi_identifier }}"

jitsi_container_labels_traefik_enabled: true
jitsi_container_labels_traefik_docker_network: "{{ jitsi_container_network }}"
jitsi_container_labels_traefik_hostname: "{{ jitsi_hostname }}"
# The path prefix must either be `/` or not end with a slash (e.g. `/jitsi`).
jitsi_container_labels_traefik_path_prefix: "{{ jitsi_path_prefix }}"
jitsi_container_labels_traefik_entrypoints: web-secure

jitsi_web_container_image: "{{ jitsi_web_container_image_registry_prefix }}jitsi/web:{{ jitsi_container_image_tag }}"
jitsi_web_container_image_force_pull: "{{ jitsi_web_container_image.endswith(':latest') }}"
jitsi_web_container_image_registry_prefix: "{{ jitsi_web_container_image_registry_prefix_upstream }}"
jitsi_web_container_image_registry_prefix_upstream: "{{ jitsi_web_container_image_registry_prefix_upstream_default }}"
jitsi_web_container_image_registry_prefix_upstream_default: docker.io/

# jitsi_web_container_labels_traefik_enabled controls whether labels to assist a Traefik reverse-proxy will be attached to the Web container.
# See `../templates/web/labels.j2` for details.
#
# To inject your own other container labels, see `jitsi_web_container_labels_additional_labels`.
jitsi_web_container_labels_traefik_enabled: "{{ jitsi_container_labels_traefik_enabled }}"
jitsi_web_container_labels_traefik_docker_network: "{{ jitsi_container_labels_traefik_docker_network }}"
jitsi_web_container_labels_traefik_hostname: "{{ jitsi_container_labels_traefik_hostname }}"
jitsi_web_container_labels_traefik_path_prefix: "{{ jitsi_container_labels_traefik_path_prefix }}"
jitsi_web_container_labels_traefik_rule: "Host(`{{ jitsi_web_container_labels_traefik_hostname }}`){% if jitsi_web_container_labels_traefik_path_prefix != '/' %} && PathPrefix(`{{ jitsi_web_container_labels_traefik_path_prefix }}`){% endif %}"
jitsi_web_container_labels_traefik_priority: 0
jitsi_web_container_labels_traefik_entrypoints: "{{ jitsi_container_labels_traefik_entrypoints }}"
jitsi_web_container_labels_traefik_tls: "{{ jitsi_web_container_labels_traefik_entrypoints != 'web' }}"
jitsi_web_container_labels_traefik_tls_certResolver: default  # noqa var-naming

# Controls which additional headers to attach to all HTTP requests.
# To add your own custom request headers, use `jitsi_web_container_labels_traefik_additional_response_headers_custom`
jitsi_web_container_labels_traefik_additional_request_headers: "{{ jitsi_web_container_labels_traefik_additional_request_headers_auto | combine(jitsi_web_container_labels_traefik_additional_request_headers_custom) }}"
jitsi_web_container_labels_traefik_additional_request_headers_auto: {}
jitsi_web_container_labels_traefik_additional_request_headers_custom: {}

# Controls which additional headers to attach to all HTTP responses.
# To add your own custom response headers, use `jitsi_web_container_labels_traefik_additional_response_headers_custom`
jitsi_web_container_labels_traefik_additional_response_headers: "{{ jitsi_web_container_labels_traefik_additional_response_headers_auto | combine(jitsi_web_container_labels_traefik_additional_response_headers_custom) }}"
jitsi_web_container_labels_traefik_additional_response_headers_auto: |
  {{
    {}
    | combine ({'X-XSS-Protection': jitsi_web_http_header_xss_protection} if jitsi_web_http_header_xss_protection else {})
    | combine ({'X-Frame-Options': jitsi_web_http_header_frame_options} if jitsi_web_http_header_frame_options else {})
    | combine ({'X-Content-Type-Options': jitsi_web_http_header_content_type_options} if jitsi_web_http_header_content_type_options else {})
    | combine ({'Content-Security-Policy': jitsi_web_http_header_content_security_policy} if jitsi_web_http_header_content_security_policy else {})
    | combine ({'Permission-Policy': jitsi_web_http_header_content_permission_policy} if jitsi_web_http_header_content_permission_policy else {})
    | combine ({'Strict-Transport-Security': jitsi_web_http_header_strict_transport_security} if jitsi_web_http_header_strict_transport_security and jitsi_web_container_labels_traefik_tls else {})
  }}
jitsi_web_container_labels_traefik_additional_response_headers_custom: {}

# jitsi_web_container_labels_additional_labels contains a multiline string with additional labels to add to the container label file.
# See `../templates/labels.j2` for details.
#
# Example:
# jitsi_web_container_labels_additional_labels: |
#   my.label=1
#   another.label="here"
jitsi_web_container_labels_additional_labels: ''

jitsi_web_base_path: "{{ jitsi_base_path }}/web"
jitsi_web_config_path: "{{ jitsi_web_base_path }}/config"
jitsi_web_transcripts_path: "{{ jitsi_web_base_path }}/transcripts"
jitsi_web_crontabs_path: "{{ jitsi_web_base_path }}/crontabs"
jitsi_web_well_known_path: "{{ jitsi_web_base_path }}/well-known"

jitsi_web_public_url: "https://{{ jitsi_hostname }}{{ '' if jitsi_path_prefix == '/' else jitsi_path_prefix }}/"

# STUN servers used in the web UI. Feel free to point them to your own STUN server.
# Addresses need to be prefixed with one of `stun:`, `turn:` or `turns:`.
jitsi_web_stun_servers: ['stun:meet-jit-si-turnrelay.jitsi.net:443']

# Controls whether Jitsi Web will allow being put in an iframe, etc.
jitsi_web_framing_enabled: false

# Specifies the value of the `X-XSS-Protection` header
# Stops pages from loading when they detect reflected cross-site scripting (XSS) attacks.
#
# Learn more about it is here:
# - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection
# - https://portswigger.net/web-security/cross-site-scripting/reflected
jitsi_web_http_header_xss_protection: "1; mode=block"

# Specifies the value of the `X-Frame-Options` header which controls whether framing can happen.
# See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
jitsi_web_http_header_frame_options: "{{ '' if jitsi_web_framing_enabled else 'SAMEORIGIN' }}"

# Specifies the value of the `X-Content-Type-Options` header.
# See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options
jitsi_web_http_header_content_type_options: nosniff

# Specifies the value of the `Content-Security-Policy` header.
# See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy
jitsi_web_http_header_content_security_policy: |-
  {{ '' if jitsi_web_framing_enabled else "frame-ancestors 'self'" }}

# Specifies the value of the `Permission-Policy` header.
# See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Permission-Policy
jitsi_web_http_header_content_permission_policy: "{{ 'interest-cohort=()' if jitsi_web_http_floc_optout_enabled else '' }}"

# Specifies the value of the `Strict-Transport-Security` header.
# See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
jitsi_web_http_header_strict_transport_security: "max-age=31536000; includeSubDomains{{ '; preload' if jitsi_web_hsts_preload_enabled else '' }}"

# Controls whether to send a "Permissions-Policy interest-cohort=();" header along with all responses
#
# Learn more about what it is here:
# - https://www.eff.org/deeplinks/2021/03/googles-floc-terrible-idea
# - https://paramdeo.com/blog/opting-your-website-out-of-googles-floc-network
# - https://amifloced.org/
#
# Of course, a better solution is to just stop using browsers (like Chrome), which participate in such tracking practices.
# See: `jitsi_web_http_header_content_permission_policy`
jitsi_web_http_floc_optout_enabled: true

# Controls if HSTS preloading is enabled
#
# In its strongest and recommended form, the [HSTS policy](https://www.chromium.org/hsts) includes all subdomains, and
# indicates a willingness to be "preloaded" into browsers:
# `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
# For more information visit:
# - https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security
# - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
# - https://hstspreload.org/#opt-in
# See: `jitsi_web_http_header_strict_transport_security`
jitsi_web_hsts_preload_enabled: false

jitsi_letsencrypt_domain: "{{ jitsi_hostname }}"
jitsi_letsencrypt_email: ''

jitsi_turn_credentials: ''
jitsi_turn_host: ''
jitsi_turns_host: ''
jitsi_turn_port: ''
jitsi_turns_port: ''
jitsi_turn_transport: tcp

# Controls whether Etherpad will be available within Jitsi
jitsi_etherpad_enabled: false
# Specifies the Etherpad base URL (e.g. https://etherpad.DOMAIN or https://hostname/etherpad)
jitsi_etherpad_base: ''

# Controls whether the jitsi-web container exposes its HTTP port (tcp/80 in the container).
#
# Takes an "<ip>:<port>" or "<port>" value (e.g. "127.0.0.1:13080"), or empty string to not expose.
jitsi_web_container_http_host_bind_port: ''

# A list of additional container networks that the Web container would be connected to.
# The role does not create these networks, so make sure they already exist.
# Use this to expose this container to another reverse proxy, which runs in a different container network.
jitsi_web_container_additional_networks: "{{ jitsi_web_container_additional_networks_auto + jitsi_web_container_additional_networks_custom }}"
jitsi_web_container_additional_networks_auto: []
jitsi_web_container_additional_networks_custom: []

# A list of extra arguments to pass to the container
jitsi_web_container_extra_arguments: []

# List of systemd services that jitsi-web.service depends on
jitsi_web_systemd_required_services_list: "{{ jitsi_web_systemd_required_services_list_default + jitsi_web_systemd_required_services_list_auto + jitsi_web_systemd_required_services_list_custom }}"
jitsi_web_systemd_required_services_list_default: "{{ [devture_systemd_docker_base_docker_service_name] if devture_systemd_docker_base_docker_service_name else [] }}"
jitsi_web_systemd_required_services_list_auto: []
jitsi_web_systemd_required_services_list_custom: []

# Custom configuration to be appended to `interface_config.js`, passed to Jitsi Web.
#
# Note: not to be confused with `jitsi_web_custom_config_extension`.
#
# For interface configuration, the flow is like this:
# - a default `interface_config.js` is generated from within the docker image
# - the contents of `jitsi_web_custom_interface_config_extension` is appended and can define new settings or override defaults.
#
# Example:
# jitsi_web_custom_interface_config_extension: |
#   interfaceConfig.LANG_DETECTION = false;
#   interfaceConfig.SHOW_JITSI_WATERMARK = false;
#   interfaceConfig.DISABLE_VIDEO_BACKGROUND = true;
jitsi_web_custom_interface_config_extension: ''


# Controls after which participant audio will be muted. If not specified, defaults to Jitsi's default value (likely 10)
jitsi_web_config_start_audio_muted_after_nth_participant: ~
# Controls after which participant video will be muted. If not specified, defaults to Jitsi's default value (likely 10)
jitsi_web_config_start_video_muted_after_nth_participant: ~

jitsi_web_config_defaultLanguage: 'en'  # noqa var-naming

# Ideal and also maximum resolution width. If not specified, defaults to Jitsi's default value (likely 1280)
jitsi_web_config_resolution_width_ideal_and_max: ~
# Minimum resolution width. If not specified, defaults to Jitsi's default value (likely 320)
jitsi_web_config_resolution_width_min: ~
# Ideal and also maximum resolution height. If not specified, defaults to Jitsi's default value (likely 720)
jitsi_web_config_resolution_height_ideal_and_max: ~
# Minimum resolution height. If not specified, defaults to Jitsi's default value (likely 180)
jitsi_web_config_resolution_height_min: ~

# Custom configuration to be injected into `custom-config.js`, passed to Jitsi Web.
# This configuration gets appended to the final configuration that Jitsi Web uses.
#
# Note: not to be confused with `jitsi_web_custom_interface_config_extension`.
#
# The flow is like this:
# - some default configuration is automatically generated based on the environment variables passed to the Jitsi Web container
# - the contents of `custom-config.js` is appended to it (see `templates/web/custom-config.js.j2`)
# - said `custom-config.js` contains your custom contents specified in `jitsi_web_custom_config_extension`.
#
# Example:
# jitsi_web_custom_config_extension: |
#   if (!config.hasOwnProperty('testing')) config.testing = {};
#   config.testing.p2pTestMode = true
jitsi_web_custom_config_extension: ''

# Additional environment variables to pass to the Jitsi Web container.
# You can use this to further influence the default configuration generated by the Jitsi Web container on every startup.
# Besides influencing the final configuration by passing environment variables, you can also inject custom configuration
# by using `jitsi_web_custom_config_extension`.
#
# Example:
# jitsi_web_environment_variables_extension: |
#   ENABLE_FILE_RECORDING_SERVICE=1
#   DROPBOX_APPKEY=something
#   DROPBOX_REDIRECT_URI=something
jitsi_web_environment_variables_extension: ''

jitsi_prosody_container_image: "{{ jitsi_prosody_container_image_registry_prefix }}jitsi/prosody:{{ jitsi_container_image_tag }}"
jitsi_prosody_container_image_force_pull: "{{ jitsi_prosody_container_image.endswith(':latest') }}"
jitsi_prosody_container_image_registry_prefix: "{{ jitsi_prosody_container_image_registry_prefix_upstream }}"
jitsi_prosody_container_image_registry_prefix_upstream: "{{ jitsi_prosody_container_image_registry_prefix_upstream_default }}"
jitsi_prosody_container_image_registry_prefix_upstream_default: docker.io/

# A list of additional container networks that the Prosody container would be connected to.
# The role does not create these networks, so make sure they already exist.
# Use this to expose this container to another reverse proxy, which runs in a different container network.
jitsi_prosody_container_additional_networks: "{{ jitsi_prosody_container_additional_networks_auto + jitsi_prosody_container_additional_networks_custom }}"
jitsi_prosody_container_additional_networks_auto: []
jitsi_prosody_container_additional_networks_custom: []

# jitsi_prosody_container_labels_traefik_enabled controls whether labels to assist a Traefik reverse-proxy will be attached to the Prosody container.
# See `../templates/web/labels.j2` for details.
#
# To inject your own other container labels, see `jitsi_prosody_container_labels_additional_labels`.
jitsi_prosody_container_labels_traefik_enabled: "{{ jitsi_container_labels_traefik_enabled }}"
jitsi_prosody_container_labels_traefik_docker_network: "{{ jitsi_container_labels_traefik_docker_network }}"
jitsi_prosody_container_labels_traefik_hostname: "{{ jitsi_container_labels_traefik_hostname }}"
jitsi_prosody_container_labels_traefik_path_prefix: "{{ jitsi_container_labels_traefik_path_prefix }}"
jitsi_prosody_container_labels_traefik_rule: "Host(`{{ jitsi_prosody_container_labels_traefik_hostname }}`) && PathPrefix(`{{ jitsi_prosody_container_labels_traefik_path_prefix }}{{ '' if jitsi_container_labels_traefik_path_prefix == '/' else '/' }}xmpp-websocket`)"
jitsi_prosody_container_labels_traefik_priority: 0
jitsi_prosody_container_labels_traefik_entrypoints: "{{ jitsi_container_labels_traefik_entrypoints }}"
jitsi_prosody_container_labels_traefik_tls: "{{ jitsi_prosody_container_labels_traefik_entrypoints != 'web' }}"
jitsi_prosody_container_labels_traefik_tls_certResolver: default  # noqa var-naming

# Controls which additional headers to attach to all HTTP requests.
# To add your own custom request headers, use `jitsi_prosody_container_labels_traefik_additional_response_headers_custom`
jitsi_prosody_container_labels_traefik_additional_request_headers: "{{ jitsi_prosody_container_labels_traefik_additional_request_headers_auto | combine(jitsi_prosody_container_labels_traefik_additional_request_headers_custom) }}"
jitsi_prosody_container_labels_traefik_additional_request_headers_auto: {}
jitsi_prosody_container_labels_traefik_additional_request_headers_custom: {}

# Controls which additional headers to attach to all HTTP responses.
# To add your own custom response headers, use `jitsi_prosody_container_labels_traefik_additional_response_headers_custom`
jitsi_prosody_container_labels_traefik_additional_response_headers: "{{ jitsi_prosody_container_labels_traefik_additional_response_headers_auto | combine(jitsi_prosody_container_labels_traefik_additional_response_headers_custom) }}"
jitsi_prosody_container_labels_traefik_additional_response_headers_auto: {}
jitsi_prosody_container_labels_traefik_additional_response_headers_custom: {}

# jitsi_prosody_container_labels_additional_labels contains a multiline string with additional labels to add to the container label file.
# See `../templates/labels.j2` for details.
#
# Example:
# jitsi_prosody_container_labels_additional_labels: |
#   my.label=1
#   another.label="here"
jitsi_prosody_container_labels_additional_labels: ''

jitsi_prosody_base_path: "{{ jitsi_base_path }}/prosody"
jitsi_prosody_config_path: "{{ jitsi_prosody_base_path }}/config"
jitsi_prosody_plugins_path: "{{ jitsi_prosody_base_path }}/prosody-plugins-custom"
jitsi_prosody_ext_path: "{{ jitsi_prosody_base_path }}/ext"

# well known is currently only needed for auth type "matrix"
jitsi_web_well_known_enabled: "{{ jitsi_web_well_known_element_jitsi_enabled }}"
jitsi_web_well_known_element_jitsi_enabled: "{{ jitsi_enable_auth | bool and jitsi_auth_type == 'matrix' }}"
jitsi_web_well_known_element_jitsi_json: '{"auth": "openidtoken-jwt"}'

jitsi_muc_modules: |
  {{
    (['matrix_power_sync'] if jitsi_prosody_auth_matrix_uvs_sync_power_levels | bool else [])
  }}

# A list of extra arguments to pass to the container
jitsi_prosody_container_extra_arguments: []

# List of systemd services that jitsi-prosody.service depends on
jitsi_prosody_systemd_required_services_list: "{{ jitsi_prosody_systemd_required_services_list_default + jitsi_prosody_systemd_required_services_list_auto + jitsi_prosody_systemd_required_services_list_custom }}"
jitsi_prosody_systemd_required_services_list_default: "{{ [devture_systemd_docker_base_docker_service_name] if devture_systemd_docker_base_docker_service_name else [] }}"
jitsi_prosody_systemd_required_services_list_auto: []
jitsi_prosody_systemd_required_services_list_custom: []

# Neccessary Port binding for those disabling the integrated nginx proxy
jitsi_prosody_container_http_host_bind_port: ''

jitsi_prosody_container_jvb_host_bind_port: ''

jitsi_jicofo_container_image: "{{ jitsi_jicofo_container_image_registry_prefix }}jitsi/jicofo:{{ jitsi_container_image_tag }}"
jitsi_jicofo_container_image_force_pull: "{{ jitsi_jicofo_container_image.endswith(':latest') }}"
jitsi_jicofo_container_image_registry_prefix: "{{ jitsi_jicofo_container_image_registry_prefix_upstream }}"
jitsi_jicofo_container_image_registry_prefix_upstream: "{{ jitsi_jicofo_container_image_registry_prefix_upstream_default }}"
jitsi_jicofo_container_image_registry_prefix_upstream_default: docker.io/

# A list of additional container networks that the Jicofo container would be connected to.
# The role does not create these networks, so make sure they already exist.
# Use this to expose this container to another reverse proxy, which runs in a different container network.
jitsi_jicofo_container_additional_networks: "{{ jitsi_jicofo_container_additional_networks_auto + jitsi_jicofo_container_additional_networks_custom }}"
jitsi_jicofo_container_additional_networks_auto: []
jitsi_jicofo_container_additional_networks_custom: []

jitsi_jicofo_base_path: "{{ jitsi_base_path }}/jicofo"
jitsi_jicofo_config_path: "{{ jitsi_jicofo_base_path }}/config"

# A list of extra arguments to pass to the container
jitsi_jicofo_container_extra_arguments: []

# List of systemd services that jitsi-jicofo.service depends on
jitsi_jicofo_systemd_required_services_list: "{{ jitsi_jicofo_systemd_required_services_list_default + jitsi_jicofo_systemd_required_services_list_auto + jitsi_jicofo_systemd_required_services_list_custom }}"
jitsi_jicofo_systemd_required_services_list_default: |
  {{
    ([devture_systemd_docker_base_docker_service_name] if devture_systemd_docker_base_docker_service_name else [])
    +
    [
      (jitsi_identifier + '-prosody.service'),
    ]
  }}
jitsi_jicofo_systemd_required_services_list_auto: []
jitsi_jicofo_systemd_required_services_list_custom: []

jitsi_jicofo_component_secret: ''
jitsi_jicofo_auth_user: focus
jitsi_jicofo_auth_password: ''

# To enable Sentry integration for Jicofo, specify a valid DSN connection string
jitsi_jicofo_sentry_dsn: ''

jitsi_jvb_container_image: "{{ jitsi_jvb_container_image_registry_prefix }}jitsi/jvb:{{ jitsi_container_image_tag }}"
jitsi_jvb_container_image_force_pull: "{{ jitsi_jvb_container_image.endswith(':latest') }}"
jitsi_jvb_container_image_registry_prefix: "{{ jitsi_jvb_container_image_registry_prefix_upstream }}"
jitsi_jvb_container_image_registry_prefix_upstream: "{{ jitsi_jvb_container_image_registry_prefix_upstream_default }}"
jitsi_jvb_container_image_registry_prefix_upstream_default: docker.io/

# A list of additional container networks that the JVB container would be connected to.
# The role does not create these networks, so make sure they already exist.
# Use this to expose this container to another reverse proxy, which runs in a different container network.
jitsi_jvb_container_additional_networks: "{{ jitsi_jvb_container_additional_networks_auto + jitsi_jvb_container_additional_networks_custom }}"
jitsi_jvb_container_additional_networks_auto: []
jitsi_jvb_container_additional_networks_custom: []

# jitsi_jvb_container_labels_traefik_enabled controls whether labels to assist a Traefik reverse-proxy will be attached to the JVB container.
# See `../templates/web/labels.j2` for details.
#
# To inject your own other container labels, see `jitsi_jvb_container_labels_additional_labels`.
jitsi_jvb_container_labels_traefik_enabled: "{{ jitsi_container_labels_traefik_enabled }}"
jitsi_jvb_container_labels_traefik_docker_network: "{{ jitsi_container_labels_traefik_docker_network }}"
jitsi_jvb_container_labels_traefik_hostname: "{{ jitsi_container_labels_traefik_hostname }}"
jitsi_jvb_container_labels_traefik_path_prefix: "{{ jitsi_container_labels_traefik_path_prefix }}"
jitsi_jvb_container_labels_traefik_rule: "Host(`{{ jitsi_jvb_container_labels_traefik_hostname }}`) && PathPrefix(`{{ jitsi_jvb_container_labels_traefik_path_prefix }}{{ '' if jitsi_container_labels_traefik_path_prefix == '/' else '/' }}colibri-ws/{{ jitsi_jvb_server_id }}/`)"
jitsi_jvb_container_labels_traefik_priority: 0
jitsi_jvb_container_labels_traefik_entrypoints: "{{ jitsi_container_labels_traefik_entrypoints }}"
jitsi_jvb_container_labels_traefik_tls: "{{ jitsi_jvb_container_labels_traefik_entrypoints != 'web' }}"
jitsi_jvb_container_labels_traefik_tls_certResolver: default  # noqa var-naming

# Controls which additional headers to attach to all HTTP requests.
# To add your own custom request headers, use `jitsi_jvb_container_labels_traefik_additional_response_headers_custom`
jitsi_jvb_container_labels_traefik_additional_request_headers: "{{ jitsi_jvb_container_labels_traefik_additional_request_headers_auto | combine(jitsi_jvb_container_labels_traefik_additional_request_headers_custom) }}"
jitsi_jvb_container_labels_traefik_additional_request_headers_auto: {}
jitsi_jvb_container_labels_traefik_additional_request_headers_custom: {}

# Controls which additional headers to attach to all HTTP responses.
# To add your own custom response headers, use `jitsi_jvb_container_labels_traefik_additional_response_headers_custom`
jitsi_jvb_container_labels_traefik_additional_response_headers: "{{ jitsi_jvb_container_labels_traefik_additional_response_headers_auto | combine(jitsi_jvb_container_labels_traefik_additional_response_headers_custom) }}"
jitsi_jvb_container_labels_traefik_additional_response_headers_auto: {}
jitsi_jvb_container_labels_traefik_additional_response_headers_custom: {}

# jitsi_jvb_container_labels_additional_labels contains a multiline string with additional labels to add to the container label file.
# See `../templates/labels.j2` for details.
#
# Example:
# jitsi_jvb_container_labels_additional_labels: |
#   my.label=1
#   another.label="here"
jitsi_jvb_container_labels_additional_labels: ''

jitsi_jvb_base_path: "{{ jitsi_base_path }}/jvb"
jitsi_jvb_config_path: "{{ jitsi_jvb_base_path }}/config"

# A list of extra arguments to pass to the container
jitsi_jvb_container_extra_arguments: []

# List of systemd services that jitsi-jvb.service depends on
jitsi_jvb_systemd_required_services_list: "{{ jitsi_jvb_systemd_required_services_list_default + jitsi_jvb_systemd_required_services_list_auto + jitsi_jvb_systemd_required_services_list_custom }}"
jitsi_jvb_systemd_required_services_list_default: "{{ [devture_systemd_docker_base_docker_service_name] if devture_systemd_docker_base_docker_service_name else [] }}"
jitsi_jvb_systemd_required_services_list_auto: []
jitsi_jvb_systemd_required_services_list_custom: []

jitsi_jvb_auth_user: jvb
jitsi_jvb_auth_password: ''

# To enable Sentry integration for JVB, specify a valid DSN connection string
jitsi_jvb_sentry_dsn: ''

# STUN servers used by JVB on the server-side, so it can discover its own external IP address.
# Pointing this to a STUN server running on the same Docker network may lead to incorrect IP address discovery.
jitsi_jvb_stun_servers: ['meet-jit-si-turnrelay.jitsi.net:443']

jitsi_jvb_brewery_muc: jvbbrewery
jitsi_jvb_rtp_udp_port: 10000
jitsi_jvb_rtp_tcp_port: 4443
jitsi_jvb_server_id: 'jvb-1'


# Custom configuration to be injected into `custom-sip-communicator.properties`, passed to Jitsi Videobridge (JVB).
# This configuration gets appended to the final configuration that Jitsi Videobridge (JVB) uses.
#
# The flow is like this:
# - some default configuration is automatically generated based on the environment variables passed to the Jitsi Videobridge (JVB) container
# - the contents of `custom-sip-communicator.properties` is appended to it (see `templates/jvb/custom-sip-communicator.properties.j2`)
# - said `custom-sip-communicator.properties` contains your custom contents specified in `jitsi_jvb_custom_config_extension`.
#
# Example:
# jitsi_jvb_custom_config_extension: |
#   org.jitsi.videobridge.xmpp.user.shard.DISABLE_CERTIFICATE_VERIFICATION=false
#   org.jitsi.videobridge.ENABLE_STATISTICS=false
jitsi_jvb_custom_config_extension: ''

# Additional environment variables to pass to the Jitsi Videobridge (JVB) container.
# You can use this to further influence the default configuration generated by the Jitsi Videobridge (JVB) container on every startup.
# Besides influencing the final configuration by passing environment variables, you can also inject custom configuration
# by using `jitsi_jvb_custom_config_extension`.
#
# Example:
# jitsi_jvb_environment_variables_extension: |
#   SOME_VARIABLE=1
#   ANOTHER_VARIABLE=something
jitsi_jvb_environment_variables_extension: ''

# Controls whether the jitsi-jvb container exposes its RTP UDP port (udp/10000 in the container).
#
# Takes an "<ip>:<port>" or "<port>" value (e.g. "127.0.0.1:10000"), or empty string to not expose.
jitsi_jvb_container_rtp_udp_host_bind_port: "{{ jitsi_jvb_rtp_udp_port }}"

# Controls whether the jitsi-jvb container exposes its RTP UDP port (udp/4443 in the container).
#
# Takes an "<ip>:<port>" or "<port>" value (e.g. "127.0.0.1:4443"), or empty string to not expose.
jitsi_jvb_container_rtp_tcp_host_bind_port: "{{ jitsi_jvb_rtp_tcp_port }}"

# Controls whether the jitsi-jvb container exposes its Colibri WebSocket port (tcp/9090 in the container).
#
# Takes an "<ip>:<port>" or "<port>" value (e.g. "127.0.0.1:12090"), or empty string to not expose.
jitsi_jvb_container_colibri_ws_host_bind_port: ''

# Allows abailty to set XMPP AUTH user if using JIGASI
jitsi_jigasi_xmpp_user: ''
# Allows abailty to set XMPP AUTH pasword if using JIGASI
jitsi_jigasi_xmpp_password: ''
````

## File: docs/configuring-jitsi.md
````markdown
<!--
SPDX-FileCopyrightText: 2020 - 2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2020 - 2024 MDAD project contributors
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 - 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Jitsi

This is an [Ansible](https://www.ansible.com/) role which installs [Jitsi](https://jitsi.org/) to run as a bunch of [Docker](https://www.docker.com/) containers wrapped in systemd services.

See the project's [documentation](https://jitsi.github.io/handbook/) to learn what Jitsi does and why it might be useful to you.

**Note**: the configuration of this role is similar to the one by [docker-jitsi-meet](https://github.com/jitsi/docker-jitsi-meet). You can refer to the official documentation for Docker deployment [here](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/).

## Prerequisites

### Check resource requirements

Before proceeding, make sure to check server's requirements recommended by [the official deployment guide](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-requirements). Please note that Jitsi Meet requires much resource (network bandwidth, RAM, and CPU) and it depends on a lot of factors how much is sufficient for you.

Jitsi Meet is scalable with multiple JVBs ([Jitsi VideoBridge](https://github.com/jitsi/jitsi-videobridge)), and it is possible to provision them on other hosts by utilizing this role on your Ansible playbook. For big meetings the official guide recommends to prefer deploying JVBs to getting huge amount of RAM on the server. See [this section](#set-up-additional-jvbs-for-more-video-conferences-optional) below for instructions about how to set up JVBs.

### Open ports

You may need to open the following ports to your server:

- `4443/tcp` — RTP media fallback over TCP
- `10000/udp` — RTP media over UDP. Depending on your firewall/NAT configuration, incoming RTP packets on port `10000` may have the external IP of your firewall as destination address, due to the usage of STUN in JVB (see [`jitsi_jvb_stun_servers`](../defaults/main.yml)).

Docker automatically opens these ports in the server's firewall, so you likely don't need to do anything. If you use another firewall in front of the server, you may need to adjust it.

To learn more, see the upstream [firewall documentation](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#external-ports).

## Adjusting the playbook configuration

To enable Jitsi with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/matrix.example.com/vars.yml` if you use the [matrix-docker-ansible-deploy (MDAD)](https://github.com/spantaleev/matrix-docker-ansible-deploy) or [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting/mash-playbook) Ansible playbook.


```yaml
########################################################################
#                                                                      #
# jitsi                                                                #
#                                                                      #
########################################################################

jitsi_enabled: true

########################################################################
#                                                                      #
# /jitsi                                                               #
#                                                                      #
########################################################################
```

### Set the hostname

**Note**: if you use the MDAD Ansible playbook, it installs Jitsi on the `jitsi.` subdomain (`jitsi.example.com`) by default, so this setting is optional.

To serve Jitsi you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
jitsi_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the Jitsi domain to your server.

### Configure Jitsi authentication and guests mode (optional)

By default the Jitsi Meet instance **does not require for anyone to log in, and is open to use without an account**. If you're fine with such an open Jitsi instance, please skip ahead.

If you would like to control who is allowed to start meetings on your instance, you'd need to enable Jitsi's authentication and optionally guests mode. With authentication enabled, all meetings have to be started by a registered user. After the meeting is started by that user, then guests are free to join. If the registered user is not yet present, they are put on hold in individual waiting rooms.

Authentication type must be one of them: `internal` (default), `jwt`, `ldap` or `matrix`. Currently, only `internal`, `ldap` and `matrix` mechanisms are supported by this role.

**Note**: authentication is not tested by playbook's self-checks. We therefore recommend that you would make sure by yourself that authentication is configured properly. To test it, start a meeting at `example.com` on your browser.

#### Authenticate using Jitsi accounts: Auth-Type `internal`

The default authentication mechanism is `internal` auth, which requires a Jitsi account to have been configured.

To enable authentication with a Jitsi account, add the following configuration to your `vars.yml` file. Make sure to replace `USERNAME_…` and `PASSWORD_…` with your own values.

```yaml
jitsi_enable_auth: true
jitsi_enable_guests: true
jitsi_prosody_auth_internal_accounts:
  - username: "USERNAME_FOR_THE_FIRST_USER_HERE"
    password: "PASSWORD_FOR_THE_FIRST_USER_HERE"
  - username: "USERNAME_FOR_THE_SECOND_USER_HERE"
    password: "PASSWORD_FOR_THE_SECOND_USER_HERE"
```

**Note**: if Jitsi account removal function is not integrated into a playbook, these accounts will not be able to be removed from the Prosody server automatically, even if they are removed from your `vars.yml` file subsequently.

#### Authenticate using LDAP: Auth-Type `ldap`

To enable authentication with LDAP, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
jitsi_enable_auth: true
jitsi_auth_type: ldap
jitsi_ldap_url: "ldap://ldap.example.com"
jitsi_ldap_base: "OU=People,DC=example.com"
#jitsi_ldap_binddn: ""
#jitsi_ldap_bindpw: ""
jitsi_ldap_filter: "uid=%u"
jitsi_ldap_auth_method: "bind"
jitsi_ldap_version: "3"
jitsi_ldap_use_tls: true
jitsi_ldap_tls_ciphers: ""
jitsi_ldap_tls_check_peer: true
jitsi_ldap_tls_cacert_file: "/etc/ssl/certs/ca-certificates.crt"
jitsi_ldap_tls_cacert_dir: "/etc/ssl/certs"
jitsi_ldap_start_tls: false
```

For more information refer to the [docker-jitsi-meet](https://github.com/jitsi/docker-jitsi-meet#authentication-using-ldap) and the [saslauthd `LDAP_SASLAUTHD`](https://github.com/winlibs/cyrus-sasl/blob/master/saslauthd/LDAP_SASLAUTHD) documentation.

#### Authenticate using Matrix OpenID: Auth-Type `matrix`

> [!WARNING]
> This probably breaks the Jitsi instance on federated Matrix rooms and does not allow sharing conference links with guests.

This authentication method requires [Matrix User Verification Service (UVS)](https://github.com/matrix-org/matrix-user-verification-service). It verifies against Matrix OpenID, and requires a user-verification-service to run. If you use the MDAD playbook, you can refer [this document](https://github.com/spantaleev/matrix-docker-ansible-deploy/blob/master/docs/configuring-playbook-user-verification-service.md) for the instruction about installing UVS.

To enable authentication with Matrix OpenID, add the following configuration to your `vars.yml` file:

```yaml
jitsi_enable_auth: true
jitsi_auth_type: matrix
```

If you install UVS in a different way than the MDAD playbook, you need to add the following configuration to your `vars.yml` file as well. Make sure to replace `UVS_AUTH_TOKEN_HERE` (auth token for Matrix User Verification Service) and `UVS_URL_HERE` (URL where Matrix User Verification Service is hosted) with your own values.

```yaml
jitsi_prosody_auth_matrix_uvs_auth_token: UVS_AUTH_TOKEN_HERE
jitsi_prosody_auth_matrix_uvs_location: UVS_URL_HERE
```

On the MDAD playbook, these two variables are specified by default, so you do not need to add them. See its [`matrix_servers`](https://github.com/spantaleev/matrix-docker-ansible-deploy/blob/master/group_vars/matrix_servers) for details.

**Notes**:
- If you enable UVS for your Matrix homeserver (Synapse), make sure that the Matrix Federation port (usually `8448`) is accessible. See [here](https://github.com/spantaleev/matrix-docker-ansible-deploy/blob/master/docs/configuring-playbook-user-verification-service.md#open-matrix-federation-port) for more information.
- See [https://github.com/matrix-org/prosody-mod-auth-matrix-user-verification](https://github.com/matrix-org/prosody-mod-auth-matrix-user-verification) for more details about the authenticaition with Matrix OpenID.

### Configure `JVB_ADVERTISE_IPS` for running behind NAT or on a LAN environment (optional)

When running Jitsi in a LAN environment, or on the public Internet via NAT, the `JVB_ADVERTISE_IPS` enviornment variable should be set.

This variable allows to control which IP addresses the JVB will advertise for WebRTC media traffic. It is necessary to set it regardless of the use of a reverse proxy, since it's the IP address that will receive the media (audio / video) and not HTTP traffic, hence it's oblivious to the reverse proxy.

If your users are coming in over the Internet (and not over LAN), this will likely be your public IP address. If this is not set up correctly, calls will crash when more than two users join a meeting.

To set the variable, add the following configuration to your `vars.yml` file. Make sure to replace `LOCAL_IP_ADDRESS_OF_THE_HOST_HERE` with a proper value.

```yaml
jitsi_jvb_container_extra_arguments:
  - '--env "JVB_ADVERTISE_IPS=LOCAL_IP_ADDRESS_OF_THE_HOST_HERE"'
```

Check [the official documentation](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#running-behind-nat-or-on-a-lan-environment) for more details about it.

### Additional configurations (optional)

#### Set a maximum number of participants on a Jitsi conference

You can set a maximum number of participants allowed to join a Jitsi conference. By default the number is not specified.

To set it, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
jitsi_prosody_max_participants: 4 # example value
```

#### Configure time zone

You can configure the time zone (default: UTC) for the Jitsi instance. To configure it, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
jitsi_timezone: America/New_York
```

#### Enable lobby

With a lobby feature enabled, before participants can join the meeting, they must ask the moderator to be let in. This is useful when you're hosting a private conference.

This feature is disabled by default. To enable it, add the following configuration to your `vars.yml` file:

```yaml
jitsi_enable_lobby: true
```

#### Disable Gravatar

In the default Jisti Meet configuration, `gravatar.com` is enabled as an avatar service.

As this will result in third party request leaking data to the Gravatar Service (`gravatar.com`, unless configured otherwise), you can disable it by adding the following configuration to your `vars.yml` file:

```yaml
jitsi_disable_gravatar: true
```

#### Control Etherpad's availability on Jitsi conferences

If the self-hosted Etherpad instance (which can be installed with [this Ansible role of MASH project](https://github.com/mother-of-all-self-hosting/ansible-role-etherpad)) is available, you can configure it so that it will show up in Jitsi conferences.

By default it is disabled, and you can enable it by adding the following configuration to your `vars.yml` file. Make sure to replace `YOUR_ETHERPAD_BASE_URL_HERE` with your Etherpad base URL (e.g. `https://etherpad.example.com` or `https://example.com/etherpad`).

```yaml
jitsi_etherpad_enabled: true
jitsi_etherpad_base: YOUR_ETHERPAD_BASE_URL_HERE
```

**Note**: on the MDAD Ansible playbook this configuration is enabled by default. See its [`matrix_servers`](https://github.com/spantaleev/matrix-docker-ansible-deploy/blob/master/group_vars/matrix_servers) for details.

#### Enable HSTS preloading

If you want to enable [HSTS (HTTP Strict-Transport-Security) preloading](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security#preloading_strict_transport_security), add the following configuration to your `vars.yml` file:

```yaml
jitsi_web_hsts_preload_enabled: true
```

#### Allow/disallow embedding Jitsi Meet

It is possible to control whether embedding Jitsi Meet to a frame on another website will be allowed or not.

By default it is disallowed, and you can allow it by adding the following configuration to your `vars.yml` file:

```yaml
jitsi_web_framing_enabled: true
```

#### Fine tune Jitsi

If you'd like to have Jitsi save up resources, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
jitsi_web_config_resolution_width_ideal_and_max: 480
jitsi_web_config_resolution_height_ideal_and_max: 240
jitsi_web_custom_config_extension: |
  config.enableLayerSuspension = true;

  config.disableAudioLevels = true;

  config.channelLastN = 4;
```

These configurations:

- **limit the maximum video resolution**, to save up resources on both server and clients
- **suspend unused video layers** until they are requested again, to save up resources on both server and clients. Read more on this feature [here](https://jitsi.org/blog/new-off-stage-layer-suppression-feature/).
- **disable audio levels** to avoid excessive refresh of the client-side page and decrease the CPU consumption involved
- **limit the number of video feeds forwarded to each client**, to save up resources on both server and clients. As clients’ bandwidth and CPU may not bear the load, use this setting to avoid lag and crashes. This feature is available by default on other webconference applications such as Office 365 Teams (the number is limited to 4). Read how it works [here](https://github.com/jitsi/jitsi-videobridge/blob/5ff195985edf46c9399dcf263cb07167f0a2c724/doc/allocation.md).

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using these variables:
  - `jitsi_web_custom_interface_config_extension`: custom configuration to be appended to `interface_config.js`, passed to Jitsi Meet
  - `jitsi_web_custom_config_extension`: custom configuration to be injected into `custom-config.js`, passed to Jitsi Meet
  - `jitsi_jvb_custom_config_extension`: custom configuration to be injected into `custom-sip-communicator.properties`, passed to Jitsi Videobridge (JVB)

### Example configurations

Here is an example set of configurations for running a Jitsi instance with:

- hostname: `call.example.com`
- authentication using a Jitsi account (username: `US3RNAME`, password: `passw0rd`)
- guests: allowed
- maximum participants: 6 people
- timezone: Europe/Sofia
- fine tuning with the configurations presented above
- other miscellaneous options (see the official Jitsi documentation [here](https://jitsi.github.io/handbook/docs/dev-guide/dev-guide-configuration) and [here](https://jitsi.github.io/handbook/docs/user-guide/user-guide-advanced))

```yaml
jitsi_enabled: true
jitsi_hostname: "call.example.com"
jitsi_enable_auth: true
jitsi_enable_guests: true
jitsi_prosody_auth_internal_accounts:
  - username: "US3RNAME"
    password: "passw0rd"
jitsi_prosody_max_participants: 6
jitsi_timezone: Europe/Sofia
jitsi_web_config_resolution_width_ideal_and_max: 480
jitsi_web_config_resolution_height_ideal_and_max: 240
jitsi_web_custom_config_extension: |
  config.enableLayerSuspension = true;
  config.disableAudioLevels = true;
  config.channelLastN = 4;
  config.requireDisplayName = true; // force users to set a display name
  config.startAudioOnly = true; // start the conference in audio only mode (no video is being received nor sent)
```

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MDAD / MASH playbook, the shortcut commands with the [`just` program](https://github.com/spantaleev/matrix-docker-ansible-deploy/blob/master/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

Open the specified URL such as `https://example.com`, and you can start a videoconference. Note that you'll need to log in to your Jitsi's account if you have configured authentication with `internal` auth.

Check [the official user guide](https://jitsi.github.io/handbook/docs/category/user-guide) for details about how to use Jitsi Meet.

### Set up additional JVBs for more video-conferences (optional)

By default, a single JVB ([Jitsi VideoBridge](https://github.com/jitsi/jitsi-videobridge)) is deployed on the same host as the main server. To allow more video-conferences to happen at the same time, you'd need to provision additional JVB services on other hosts.

These settings below will allow you to provision those extra JVB instances. The instances will register themselves with the Prosody service, and be available for Jicofo to route conferences too.

#### Add the `jitsi_jvb_servers` section on `hosts` file

For additional JVBs, you'd need to add the section titled `jitsi_jvb_servers` on the ansible `hosts` file with the details of the JVB hosts as below:

```INI
[jitsi_jvb_servers]
jvb-2.example.com ansible_host=192.168.0.2
```

Make sure to replace `jvb-2.example.com` with your hostname for the JVB and `192.168.0.2` with your JVB's external IP address, respectively.

You could add JVB hosts as many as you would like. When doing so, add lines with the details of them.

#### Prepare `vars.yml` files for additional JVBs

If the main server is `example.com` and the additional JVB instance is going to be deployed at `jvb-2.example.com`, the variables for the latter need to be specified on `vars.yml` in its directory (`inventory/host_vars/jvb-2.example.com`).

Note that most (if not all) variables are common for both servers.

If you are setting up multiple JVB instances, you'd need to create `vars.yml` files for each of them too (`inventory/host_vars/jvb-3.example.com/vars.yml`, for example).

#### Set the server ID to each JVB

Each JVB requires a server ID to be set, so that it will be uniquely identified. The server ID allows Jitsi to keep track of which conferences are on which JVB.

The server ID can be set with the variable `jitsi_jvb_server_id`. It will end up as the `JVB_WS_SERVER_ID` environment variables in the JVB docker container.

To set the server ID to `jvb-2`, add the following configuration to either `hosts` or `vars.yml` files (adapt to your needs).

- On `hosts`:

  Add `jitsi_jvb_server_id=jvb-2` after your JVB's external IP addresses as below:

  ```INI
  [jitsi_jvb_servers]
  jvb-2.example.com ansible_host=192.168.0.2 jitsi_jvb_server_id=jvb-2
  jvb-3.example.com ansible_host=192.168.0.3 jitsi_jvb_server_id=jvb-2
  ```

- On `vars.yml` files:

  ```yaml
  jitsi_jvb_server_id: 'jvb-2'
  ```

Alternatively, you can specify the variable as a parameter to [the ansible command](#run-the-playbook).

**Note**: the server ID `jvb-1` is reserved for the JVB instance running on the main host, therefore should not be used as the ID of an additional JVB host.

#### Set colibri WebSocket port

The additional JVBs will need to expose the colibri WebSocket port.

To expose the port, add the following configuration to your `vars.yml` files:

```yaml
jitsi_jvb_container_colibri_ws_host_bind_port: 9090
```

#### Set Prosody XMPP server

The JVB will also need to know the location of the Prosody XMPP server.

Similar to the server ID (`jitsi_jvb_server_id`), this can be set with the variable for the JVB by using the variable `jitsi_xmpp_server`.

##### Set the main server's hostname

The Jitsi Prosody container is deployed on the main server by default, so the value can be set to the its domain. To set the value, add the following configuration to your `vars.yml` files (adapt to your needs):

```yaml
jitsi_xmpp_server: "example.com"
```

##### Set an IP address of the main server

Alternatively, the IP address of the main server can be set. This can be useful if you would like to use a private IP address.

To set the IP address of the server, add the following configuration to your `vars.yml` files:

```yaml
jitsi_xmpp_server: "192.168.0.1"
```

##### Expose XMPP port

By default, the main server does not expose the XMPP port (`5222`); only the XMPP container exposes it internally inside the host. This means that the first JVB (which runs on the main server) can reach it but the additional JVBs cannot. Therefore, the XMPP server needs to expose the port, so that the additional JVBs can connect to it.

To expose the port and have Docker forward the port, add the following configuration to your `vars.yml` files:

```yaml
jitsi_prosody_container_jvb_host_bind_port: 5222
```

#### Reverse-proxy with Traefik

To make Traefik reverse-proxy to these additional JVBs, add the following configuration to your main `vars.yml` file (`inventory/host_vars/example.com/vars.yml`):

```yaml
# Traefik proxying for additional JVBs. These can't be configured using Docker
# labels, like the first JVB is, because they run on different hosts, so we add
# the necessary configuration to the file provider.
traefik_provider_configuration_extension_yaml: |
  http:
   routers:
     {% for host in groups['jitsi_jvb_servers'] %}

     additional-{{ hostvars[host]['jitsi_jvb_server_id'] }}-router:
       entryPoints:
         - "{{ traefik_entrypoint_primary }}"
       rule: "Host(`{{ jitsi_hostname }}`) && PathPrefix(`/colibri-ws/{{ hostvars[host]['jitsi_jvb_server_id'] }}/`)"
       service: additional-{{ hostvars[host]['jitsi_jvb_server_id'] }}-service
       {% if traefik_entrypoint_primary != 'web' %}

       tls:
         certResolver: "{{ traefik_certResolver_primary }}"

       {% endif %}

     {% endfor %}

   services:
     {% for host in groups['jitsi_jvb_servers'] %}

     additional-{{ hostvars[host]['jitsi_jvb_server_id'] }}-service:
       loadBalancer:
         servers:
           - url: "http://{{ host }}:9090/"

     {% endfor %}
```

#### Run the playbook

After configuring `hosts` and `vars.yml` files, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts --limit jitsi_jvb_servers jitsi_jvb.yml --tags=common,setup-additional-jitsi-jvb,start
```

## Troubleshooting

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running the commands below:
- `journalctl -fu jitsi-web`
- `journalctl -fu jitsi-prosody`
- `journalctl -fu jitsi-jicofo`
- `journalctl -fu jitsi-jvb`

**Note**: these service names depend on how you/your playbook named the service, e.g. `matrix-jitsi-web`.

### `Error: Account creation/modification not supported`

If you get an error like `Error: Account creation/modification not supported` with authentication enabled, it's likely that you had previously installed Jitsi without auth/guest support.

In this case, you should consider to rebuild your Jitsi installation.

### Rebuilding your Jitsi installation

If you ever run into any trouble or if you have changed configuration (`jitsi_*` variables) too much, you can rebuild your Jitsi installation.

We normally don't recommend manual intervention, but Jitsi services tend to generate a lot of configuration files, and it is often wise to start afresh setting the services up, rather than messing with the existing configuration files. Since not all of those files are managed by Ansible (at least not yet), you may sometimes need to delete them by yourself manually.

To rebuild your Jitsi configuration, follow the procedure below:

- run this command locally to stop all Jitsi services: `just run-tags stop-group --extra-vars=group=jitsi`
- log in the server with SSH
- run this command remotely to remove all Jitsi configuration & data: `rm -rf /DIRECTORY_BY_PLAYBOOK_HERE/jitsi`
- run this command locally to set up Jitsi anew and restart services: `just install-service jitsi`

Replace `DIRECTORY_BY_PLAYBOOK_HERE` with the directory which your playbook has created. For example, it is `mash` if you use mash-playbook, and it is `matrix` if MDAD playbook.
````

## File: LICENSES/AGPL-3.0-or-later.txt
````
GNU AFFERO GENERAL PUBLIC LICENSE
Version 3, 19 November 2007

Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>

Everyone is permitted to copy and distribute verbatim copies of this license document, but changing it is not allowed.

                            Preamble

The GNU Affero General Public License is a free, copyleft license for software and other kinds of works, specifically designed to ensure cooperation with the community in the case of network server software.

The licenses for most software and other practical works are designed to take away your freedom to share and change the works.  By contrast, our General Public Licenses are intended to guarantee your freedom to share and change all versions of a program--to make sure it remains free software for all its users.

When we speak of free software, we are referring to freedom, not price.  Our General Public Licenses are designed to make sure that you have the freedom to distribute copies of free software (and charge for them if you wish), that you receive source code or can get it if you want it, that you can change the software or use pieces of it in new free programs, and that you know you can do these things.

Developers that use our General Public Licenses protect your rights with two steps: (1) assert copyright on the software, and (2) offer you this License which gives you legal permission to copy, distribute and/or modify the software.

A secondary benefit of defending all users' freedom is that improvements made in alternate versions of the program, if they receive widespread use, become available for other developers to incorporate.  Many developers of free software are heartened and encouraged by the resulting cooperation.  However, in the case of software used on network servers, this result may fail to come about. The GNU General Public License permits making a modified version and letting the public access it on a server without ever releasing its source code to the public.

The GNU Affero General Public License is designed specifically to ensure that, in such cases, the modified source code becomes available to the community.  It requires the operator of a network server to provide the source code of the modified version running there to the users of that server.  Therefore, public use of a modified version, on a publicly accessible server, gives the public access to the source code of the modified version.

An older license, called the Affero General Public License and published by Affero, was designed to accomplish similar goals.  This is a different license, not a version of the Affero GPL, but Affero has released a new version of the Affero GPL which permits relicensing under this license.

The precise terms and conditions for copying, distribution and modification follow.

                       TERMS AND CONDITIONS

0. Definitions.

"This License" refers to version 3 of the GNU Affero General Public License.

"Copyright" also means copyright-like laws that apply to other kinds of works, such as semiconductor masks.

"The Program" refers to any copyrightable work licensed under this License.  Each licensee is addressed as "you".  "Licensees" and "recipients" may be individuals or organizations.

To "modify" a work means to copy from or adapt all or part of the work in a fashion requiring copyright permission, other than the making of an exact copy.  The resulting work is called a "modified version" of the earlier work or a work "based on" the earlier work.

A "covered work" means either the unmodified Program or a work based on the Program.

To "propagate" a work means to do anything with it that, without permission, would make you directly or secondarily liable for infringement under applicable copyright law, except executing it on a computer or modifying a private copy.  Propagation includes copying, distribution (with or without modification), making available to the public, and in some countries other activities as well.

To "convey" a work means any kind of propagation that enables other parties to make or receive copies.  Mere interaction with a user through a computer network, with no transfer of a copy, is not conveying.

An interactive user interface displays "Appropriate Legal Notices" to the extent that it includes a convenient and prominently visible feature that (1) displays an appropriate copyright notice, and (2) tells the user that there is no warranty for the work (except to the extent that warranties are provided), that licensees may convey the work under this License, and how to view a copy of this License.  If the interface presents a list of user commands or options, such as a menu, a prominent item in the list meets this criterion.

1. Source Code.
The "source code" for a work means the preferred form of the work for making modifications to it.  "Object code" means any non-source form of a work.

A "Standard Interface" means an interface that either is an official standard defined by a recognized standards body, or, in the case of interfaces specified for a particular programming language, one that is widely used among developers working in that language.

The "System Libraries" of an executable work include anything, other than the work as a whole, that (a) is included in the normal form of packaging a Major Component, but which is not part of that Major Component, and (b) serves only to enable use of the work with that Major Component, or to implement a Standard Interface for which an implementation is available to the public in source code form.  A "Major Component", in this context, means a major essential component (kernel, window system, and so on) of the specific operating system (if any) on which the executable work runs, or a compiler used to produce the work, or an object code interpreter used to run it.

The "Corresponding Source" for a work in object code form means all the source code needed to generate, install, and (for an executable work) run the object code and to modify the work, including scripts to control those activities.  However, it does not include the work's System Libraries, or general-purpose tools or generally available free programs which are used unmodified in performing those activities but which are not part of the work.  For example, Corresponding Source includes interface definition files associated with source files for the work, and the source code for shared libraries and dynamically linked subprograms that the work is specifically designed to require, such as by intimate data communication or control flow between those
subprograms and other parts of the work.

The Corresponding Source need not include anything that users can regenerate automatically from other parts of the Corresponding Source.

The Corresponding Source for a work in source code form is that same work.

2. Basic Permissions.
All rights granted under this License are granted for the term of copyright on the Program, and are irrevocable provided the stated conditions are met.  This License explicitly affirms your unlimited permission to run the unmodified Program.  The output from running a covered work is covered by this License only if the output, given its content, constitutes a covered work.  This License acknowledges your rights of fair use or other equivalent, as provided by copyright law.

You may make, run and propagate covered works that you do not convey, without conditions so long as your license otherwise remains in force.  You may convey covered works to others for the sole purpose of having them make modifications exclusively for you, or provide you with facilities for running those works, provided that you comply with the terms of this License in conveying all material for which you do not control copyright.  Those thus making or running the covered works for you must do so exclusively on your behalf, under your direction and control, on terms that prohibit them from making any copies of your copyrighted material outside their relationship with you.

Conveying under any other circumstances is permitted solely under the conditions stated below.  Sublicensing is not allowed; section 10 makes it unnecessary.

3. Protecting Users' Legal Rights From Anti-Circumvention Law.
No covered work shall be deemed part of an effective technological measure under any applicable law fulfilling obligations under article 11 of the WIPO copyright treaty adopted on 20 December 1996, or similar laws prohibiting or restricting circumvention of such measures.

When you convey a covered work, you waive any legal power to forbid circumvention of technological measures to the extent such circumvention is effected by exercising rights under this License with respect to the covered work, and you disclaim any intention to limit operation or modification of the work as a means of enforcing, against the work's users, your or third parties' legal rights to forbid circumvention of technological measures.

4. Conveying Verbatim Copies.
You may convey verbatim copies of the Program's source code as you receive it, in any medium, provided that you conspicuously and appropriately publish on each copy an appropriate copyright notice; keep intact all notices stating that this License and any non-permissive terms added in accord with section 7 apply to the code; keep intact all notices of the absence of any warranty; and give all recipients a copy of this License along with the Program.

You may charge any price or no price for each copy that you convey, and you may offer support or warranty protection for a fee.

5. Conveying Modified Source Versions.
You may convey a work based on the Program, or the modifications to produce it from the Program, in the form of source code under the terms of section 4, provided that you also meet all of these conditions:

    a) The work must carry prominent notices stating that you modified it, and giving a relevant date.

    b) The work must carry prominent notices stating that it is released under this License and any conditions added under section 7.  This requirement modifies the requirement in section 4 to "keep intact all notices".

    c) You must license the entire work, as a whole, under this License to anyone who comes into possession of a copy.  This License will therefore apply, along with any applicable section 7 additional terms, to the whole of the work, and all its parts, regardless of how they are packaged.  This License gives no permission to license the work in any other way, but it does not invalidate such permission if you have separately received it.

    d) If the work has interactive user interfaces, each must display Appropriate Legal Notices; however, if the Program has interactive interfaces that do not display Appropriate Legal Notices, your work need not make them do so.

A compilation of a covered work with other separate and independent works, which are not by their nature extensions of the covered work, and which are not combined with it such as to form a larger program, in or on a volume of a storage or distribution medium, is called an "aggregate" if the compilation and its resulting copyright are not used to limit the access or legal rights of the compilation's users beyond what the individual works permit.  Inclusion of a covered work in an aggregate does not cause this License to apply to the other parts of the aggregate.

6. Conveying Non-Source Forms.
You may convey a covered work in object code form under the terms of sections 4 and 5, provided that you also convey the machine-readable Corresponding Source under the terms of this License, in one of these ways:

    a) Convey the object code in, or embodied in, a physical product (including a physical distribution medium), accompanied by the Corresponding Source fixed on a durable physical medium customarily used for software interchange.

    b) Convey the object code in, or embodied in, a physical product (including a physical distribution medium), accompanied by a written offer, valid for at least three years and valid for as long as you offer spare parts or customer support for that product model, to give anyone who possesses the object code either (1) a copy of the Corresponding Source for all the software in the product that is covered by this License, on a durable physical medium customarily used for software interchange, for a price no more than your reasonable cost of physically performing this conveying of source, or (2) access to copy the Corresponding Source from a network server at no charge.

    c) Convey individual copies of the object code with a copy of the written offer to provide the Corresponding Source.  This alternative is allowed only occasionally and noncommercially, and only if you received the object code with such an offer, in accord with subsection 6b.

    d) Convey the object code by offering access from a designated place (gratis or for a charge), and offer equivalent access to the Corresponding Source in the same way through the same place at no further charge.  You need not require recipients to copy the Corresponding Source along with the object code.  If the place to copy the object code is a network server, the Corresponding Source may be on a different server (operated by you or a third party) that supports equivalent copying facilities, provided you maintain clear directions next to the object code saying where to find the Corresponding Source.  Regardless of what server hosts the Corresponding Source, you remain obligated to ensure that it is available for as long as needed to satisfy these requirements.

    e) Convey the object code using peer-to-peer transmission, provided you inform other peers where the object code and Corresponding Source of the work are being offered to the general public at no charge under subsection 6d.

A separable portion of the object code, whose source code is excluded from the Corresponding Source as a System Library, need not be included in conveying the object code work.

A "User Product" is either (1) a "consumer product", which means any tangible personal property which is normally used for personal, family, or household purposes, or (2) anything designed or sold for incorporation into a dwelling.  In determining whether a product is a consumer product, doubtful cases shall be resolved in favor of coverage.  For a particular product received by a particular user, "normally used" refers to a typical or common use of that class of product, regardless of the status of the particular user or of the way in which the particular user actually uses, or expects or is expected to use, the product.  A product is a consumer product regardless of whether the product has substantial commercial, industrial or non-consumer uses, unless such uses represent the only significant mode of use of the product.

"Installation Information" for a User Product means any methods, procedures, authorization keys, or other information required to install and execute modified versions of a covered work in that User Product from a modified version of its Corresponding Source.  The information must suffice to ensure that the continued functioning of the modified object code is in no case prevented or interfered with solely because modification has been made.

If you convey an object code work under this section in, or with, or specifically for use in, a User Product, and the conveying occurs as part of a transaction in which the right of possession and use of the User Product is transferred to the recipient in perpetuity or for a fixed term (regardless of how the transaction is characterized), the Corresponding Source conveyed under this section must be accompanied by the Installation Information.  But this requirement does not apply if neither you nor any third party retains the ability to install modified object code on the User Product (for example, the work has been installed in ROM).

The requirement to provide Installation Information does not include a requirement to continue to provide support service, warranty, or updates for a work that has been modified or installed by the recipient, or for the User Product in which it has been modified or installed.  Access to a network may be denied when the modification itself materially and adversely affects the operation of the network or violates the rules and protocols for communication across the network.

Corresponding Source conveyed, and Installation Information provided, in accord with this section must be in a format that is publicly documented (and with an implementation available to the public in source code form), and must require no special password or key for unpacking, reading or copying.

7. Additional Terms.
"Additional permissions" are terms that supplement the terms of this License by making exceptions from one or more of its conditions. Additional permissions that are applicable to the entire Program shall be treated as though they were included in this License, to the extent that they are valid under applicable law.  If additional permissions apply only to part of the Program, that part may be used separately under those permissions, but the entire Program remains governed by this License without regard to the additional permissions.

When you convey a copy of a covered work, you may at your option remove any additional permissions from that copy, or from any part of it.  (Additional permissions may be written to require their own removal in certain cases when you modify the work.)  You may place additional permissions on material, added by you to a covered work, for which you have or can give appropriate copyright permission.

Notwithstanding any other provision of this License, for material you add to a covered work, you may (if authorized by the copyright holders of that material) supplement the terms of this License with terms:

    a) Disclaiming warranty or limiting liability differently from the terms of sections 15 and 16 of this License; or

    b) Requiring preservation of specified reasonable legal notices or author attributions in that material or in the Appropriate Legal Notices displayed by works containing it; or

    c) Prohibiting misrepresentation of the origin of that material, or requiring that modified versions of such material be marked in reasonable ways as different from the original version; or

    d) Limiting the use for publicity purposes of names of licensors or authors of the material; or

    e) Declining to grant rights under trademark law for use of some trade names, trademarks, or service marks; or

    f) Requiring indemnification of licensors and authors of that material by anyone who conveys the material (or modified versions of it) with contractual assumptions of liability to the recipient, for any liability that these contractual assumptions directly impose on those licensors and authors.

All other non-permissive additional terms are considered "further restrictions" within the meaning of section 10.  If the Program as you received it, or any part of it, contains a notice stating that it is governed by this License along with a term that is a further restriction, you may remove that term.  If a license document contains a further restriction but permits relicensing or conveying under this License, you may add to a covered work material governed by the terms of that license document, provided that the further restriction does not survive such relicensing or conveying.

If you add terms to a covered work in accord with this section, you must place, in the relevant source files, a statement of the additional terms that apply to those files, or a notice indicating where to find the applicable terms.

Additional terms, permissive or non-permissive, may be stated in the form of a separately written license, or stated as exceptions; the above requirements apply either way.

8. Termination.

You may not propagate or modify a covered work except as expressly provided under this License.  Any attempt otherwise to propagate or modify it is void, and will automatically terminate your rights under this License (including any patent licenses granted under the third paragraph of section 11).

However, if you cease all violation of this License, then your license from a particular copyright holder is reinstated (a) provisionally, unless and until the copyright holder explicitly and finally terminates your license, and (b) permanently, if the copyright holder fails to notify you of the violation by some reasonable means prior to 60 days after the cessation.

Moreover, your license from a particular copyright holder is reinstated permanently if the copyright holder notifies you of the violation by some reasonable means, this is the first time you have received notice of violation of this License (for any work) from that copyright holder, and you cure the violation prior to 30 days after your receipt of the notice.

Termination of your rights under this section does not terminate the licenses of parties who have received copies or rights from you under this License.  If your rights have been terminated and not permanently reinstated, you do not qualify to receive new licenses for the same material under section 10.

9. Acceptance Not Required for Having Copies.

You are not required to accept this License in order to receive or run a copy of the Program.  Ancillary propagation of a covered work occurring solely as a consequence of using peer-to-peer transmission to receive a copy likewise does not require acceptance.  However, nothing other than this License grants you permission to propagate or modify any covered work.  These actions infringe copyright if you do not accept this License.  Therefore, by modifying or propagating a covered work, you indicate your acceptance of this License to do so.

10. Automatic Licensing of Downstream Recipients.

Each time you convey a covered work, the recipient automatically receives a license from the original licensors, to run, modify and propagate that work, subject to this License.  You are not responsible for enforcing compliance by third parties with this License.

An "entity transaction" is a transaction transferring control of an organization, or substantially all assets of one, or subdividing an organization, or merging organizations.  If propagation of a covered work results from an entity transaction, each party to that transaction who receives a copy of the work also receives whatever licenses to the work the party's predecessor in interest had or could give under the previous paragraph, plus a right to possession of the Corresponding Source of the work from the predecessor in interest, if the predecessor has it or can get it with reasonable efforts.

You may not impose any further restrictions on the exercise of the rights granted or affirmed under this License.  For example, you may not impose a license fee, royalty, or other charge for exercise of rights granted under this License, and you may not initiate litigation (including a cross-claim or counterclaim in a lawsuit) alleging that any patent claim is infringed by making, using, selling, offering for sale, or importing the Program or any portion of it.

11. Patents.

A "contributor" is a copyright holder who authorizes use under this License of the Program or a work on which the Program is based.  The work thus licensed is called the contributor's "contributor version".

A contributor's "essential patent claims" are all patent claims owned or controlled by the contributor, whether already acquired or hereafter acquired, that would be infringed by some manner, permitted by this License, of making, using, or selling its contributor version, but do not include claims that would be infringed only as a consequence of further modification of the contributor version.  For purposes of this definition, "control" includes the right to grant patent sublicenses in a manner consistent with the requirements of this License.

Each contributor grants you a non-exclusive, worldwide, royalty-free patent license under the contributor's essential patent claims, to make, use, sell, offer for sale, import and otherwise run, modify and propagate the contents of its contributor version.

In the following three paragraphs, a "patent license" is any express agreement or commitment, however denominated, not to enforce a patent (such as an express permission to practice a patent or covenant not to sue for patent infringement).  To "grant" such a patent license to a party means to make such an agreement or commitment not to enforce a patent against the party.

If you convey a covered work, knowingly relying on a patent license, and the Corresponding Source of the work is not available for anyone to copy, free of charge and under the terms of this License, through a publicly available network server or other readily accessible means, then you must either (1) cause the Corresponding Source to be so available, or (2) arrange to deprive yourself of the benefit of the patent license for this particular work, or (3) arrange, in a manner consistent with the requirements of this License, to extend the patent
license to downstream recipients.  "Knowingly relying" means you have actual knowledge that, but for the patent license, your conveying the covered work in a country, or your recipient's use of the covered work in a country, would infringe one or more identifiable patents in that country that you have reason to believe are valid.

If, pursuant to or in connection with a single transaction or arrangement, you convey, or propagate by procuring conveyance of, a covered work, and grant a patent license to some of the parties receiving the covered work authorizing them to use, propagate, modify or convey a specific copy of the covered work, then the patent license you grant is automatically extended to all recipients of the covered work and works based on it.

A patent license is "discriminatory" if it does not include within the scope of its coverage, prohibits the exercise of, or is conditioned on the non-exercise of one or more of the rights that are specifically granted under this License.  You may not convey a covered work if you are a party to an arrangement with a third party that is in the business of distributing software, under which you make payment to the third party based on the extent of your activity of conveying the work, and under which the third party grants, to any of the parties who would receive the covered work from you, a discriminatory patent license (a) in connection with copies of the covered work conveyed by you (or copies made from those copies), or (b) primarily for and in connection with specific products or compilations that contain the covered work, unless you entered into that arrangement, or that patent license was granted, prior to 28 March 2007.

Nothing in this License shall be construed as excluding or limiting any implied license or other defenses to infringement that may otherwise be available to you under applicable patent law.

12. No Surrender of Others' Freedom.

If conditions are imposed on you (whether by court order, agreement or otherwise) that contradict the conditions of this License, they do not excuse you from the conditions of this License.  If you cannot convey a covered work so as to satisfy simultaneously your obligations under this License and any other pertinent obligations, then as a consequence you may
not convey it at all.  For example, if you agree to terms that obligate you to collect a royalty for further conveying from those to whom you convey the Program, the only way you could satisfy both those terms and this License would be to refrain entirely from conveying the Program.

13. Remote Network Interaction; Use with the GNU General Public License.

Notwithstanding any other provision of this License, if you modify the Program, your modified version must prominently offer all users interacting with it remotely through a computer network (if your version supports such interaction) an opportunity to receive the Corresponding Source of your version by providing access to the Corresponding Source from a network server at no charge, through some standard or customary means of facilitating copying of software.  This Corresponding Source shall include the Corresponding Source for any work covered by version 3 of the GNU General Public License that is incorporated pursuant to the following paragraph.

Notwithstanding any other provision of this License, you have permission to link or combine any covered work with a work licensed under version 3 of the GNU General Public License into a single combined work, and to convey the resulting work.  The terms of this License will continue to apply to the part which is the covered work, but the work with which it is combined will remain governed by version 3 of the GNU General Public License.

14. Revised Versions of this License.

The Free Software Foundation may publish revised and/or new versions of the GNU Affero General Public License from time to time.  Such new versions will be similar in spirit to the present version, but may differ in detail to address new problems or concerns.

Each version is given a distinguishing version number.  If the Program specifies that a certain numbered version of the GNU Affero General Public License "or any later version" applies to it, you have the option of following the terms and conditions either of that numbered version or of any later version published by the Free Software Foundation.  If the Program does not specify a version number of the GNU Affero General Public License, you may choose any version ever published by the Free Software Foundation.

If the Program specifies that a proxy can decide which future versions of the GNU Affero General Public License can be used, that proxy's public statement of acceptance of a version permanently authorizes you to choose that version for the Program.

Later license versions may give you additional or different permissions.  However, no additional obligations are imposed on any author or copyright holder as a result of your choosing to follow a later version.

15. Disclaimer of Warranty.

THERE IS NO WARRANTY FOR THE PROGRAM, TO THE EXTENT PERMITTED BY APPLICABLE LAW.  EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT HOLDERS AND/OR OTHER PARTIES PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  THE ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE PROGRAM IS WITH YOU.  SHOULD THE PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY SERVICING, REPAIR OR CORRECTION.

16. Limitation of Liability.

IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR CONVEYS THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES, INCLUDING ANY GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED TO LOSS OF DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR THIRD PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER PROGRAMS), EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.

17. Interpretation of Sections 15 and 16.

If the disclaimer of warranty and limitation of liability provided above cannot be given local legal effect according to their terms, reviewing courts shall apply local law that most closely approximates an absolute waiver of all civil liability in connection with the Program, unless a warranty or assumption of liability accompanies a copy of the Program in return for a fee.

END OF TERMS AND CONDITIONS

            How to Apply These Terms to Your New Programs

If you develop a new program, and you want it to be of the greatest possible use to the public, the best way to achieve this is to make it free software which everyone can redistribute and change under these terms.

To do so, attach the following notices to the program.  It is safest to attach them to the start of each source file to most effectively state the exclusion of warranty; and each file should have at least the "copyright" line and a pointer to where the full notice is found.

     <one line to give the program's name and a brief idea of what it does.>
     Copyright (C) <year>  <name of author>

     This program is free software: you can redistribute it and/or modify it under the terms of the GNU Affero General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

     This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU Affero General Public License for more details.

     You should have received a copy of the GNU Affero General Public License along with this program.  If not, see <http://www.gnu.org/licenses/>.

Also add information on how to contact you by electronic and paper mail.

If your software can interact with users remotely through a computer network, you should also make sure that it provides a way for users to get its source.  For example, if your program is a web application, its interface could display a "Source" link that leads users to an archive of the code.  There are many ways you could offer source, and different solutions will be better for different programs; see section 13 for the specific requirements.

You should also get your employer (if you work as a programmer) or school, if any, to sign a "copyright disclaimer" for the program, if necessary. For more information on this, and how to apply and follow the GNU AGPL, see <http://www.gnu.org/licenses/>.
````

## File: LICENSES/CC0-1.0.txt
````
Creative Commons Legal Code

CC0 1.0 Universal

    CREATIVE COMMONS CORPORATION IS NOT A LAW FIRM AND DOES NOT PROVIDE
    LEGAL SERVICES. DISTRIBUTION OF THIS DOCUMENT DOES NOT CREATE AN
    ATTORNEY-CLIENT RELATIONSHIP. CREATIVE COMMONS PROVIDES THIS
    INFORMATION ON AN "AS-IS" BASIS. CREATIVE COMMONS MAKES NO WARRANTIES
    REGARDING THE USE OF THIS DOCUMENT OR THE INFORMATION OR WORKS
    PROVIDED HEREUNDER, AND DISCLAIMS LIABILITY FOR DAMAGES RESULTING FROM
    THE USE OF THIS DOCUMENT OR THE INFORMATION OR WORKS PROVIDED
    HEREUNDER.

Statement of Purpose

The laws of most jurisdictions throughout the world automatically confer
exclusive Copyright and Related Rights (defined below) upon the creator
and subsequent owner(s) (each and all, an "owner") of an original work of
authorship and/or a database (each, a "Work").

Certain owners wish to permanently relinquish those rights to a Work for
the purpose of contributing to a commons of creative, cultural and
scientific works ("Commons") that the public can reliably and without fear
of later claims of infringement build upon, modify, incorporate in other
works, reuse and redistribute as freely as possible in any form whatsoever
and for any purposes, including without limitation commercial purposes.
These owners may contribute to the Commons to promote the ideal of a free
culture and the further production of creative, cultural and scientific
works, or to gain reputation or greater distribution for their Work in
part through the use and efforts of others.

For these and/or other purposes and motivations, and without any
expectation of additional consideration or compensation, the person
associating CC0 with a Work (the "Affirmer"), to the extent that he or she
is an owner of Copyright and Related Rights in the Work, voluntarily
elects to apply CC0 to the Work and publicly distribute the Work under its
terms, with knowledge of his or her Copyright and Related Rights in the
Work and the meaning and intended legal effect of CC0 on those rights.

1. Copyright and Related Rights. A Work made available under CC0 may be
protected by copyright and related or neighboring rights ("Copyright and
Related Rights"). Copyright and Related Rights include, but are not
limited to, the following:

  i. the right to reproduce, adapt, distribute, perform, display,
     communicate, and translate a Work;
 ii. moral rights retained by the original author(s) and/or performer(s);
iii. publicity and privacy rights pertaining to a person's image or
     likeness depicted in a Work;
 iv. rights protecting against unfair competition in regards to a Work,
     subject to the limitations in paragraph 4(a), below;
  v. rights protecting the extraction, dissemination, use and reuse of data
     in a Work;
 vi. database rights (such as those arising under Directive 96/9/EC of the
     European Parliament and of the Council of 11 March 1996 on the legal
     protection of databases, and under any national implementation
     thereof, including any amended or successor version of such
     directive); and
vii. other similar, equivalent or corresponding rights throughout the
     world based on applicable law or treaty, and any national
     implementations thereof.

2. Waiver. To the greatest extent permitted by, but not in contravention
of, applicable law, Affirmer hereby overtly, fully, permanently,
irrevocably and unconditionally waives, abandons, and surrenders all of
Affirmer's Copyright and Related Rights and associated claims and causes
of action, whether now known or unknown (including existing as well as
future claims and causes of action), in the Work (i) in all territories
worldwide, (ii) for the maximum duration provided by applicable law or
treaty (including future time extensions), (iii) in any current or future
medium and for any number of copies, and (iv) for any purpose whatsoever,
including without limitation commercial, advertising or promotional
purposes (the "Waiver"). Affirmer makes the Waiver for the benefit of each
member of the public at large and to the detriment of Affirmer's heirs and
successors, fully intending that such Waiver shall not be subject to
revocation, rescission, cancellation, termination, or any other legal or
equitable action to disrupt the quiet enjoyment of the Work by the public
as contemplated by Affirmer's express Statement of Purpose.

3. Public License Fallback. Should any part of the Waiver for any reason
be judged legally invalid or ineffective under applicable law, then the
Waiver shall be preserved to the maximum extent permitted taking into
account Affirmer's express Statement of Purpose. In addition, to the
extent the Waiver is so judged Affirmer hereby grants to each affected
person a royalty-free, non transferable, non sublicensable, non exclusive,
irrevocable and unconditional license to exercise Affirmer's Copyright and
Related Rights in the Work (i) in all territories worldwide, (ii) for the
maximum duration provided by applicable law or treaty (including future
time extensions), (iii) in any current or future medium and for any number
of copies, and (iv) for any purpose whatsoever, including without
limitation commercial, advertising or promotional purposes (the
"License"). The License shall be deemed effective as of the date CC0 was
applied by Affirmer to the Work. Should any part of the License for any
reason be judged legally invalid or ineffective under applicable law, such
partial invalidity or ineffectiveness shall not invalidate the remainder
of the License, and in such case Affirmer hereby affirms that he or she
will not (i) exercise any of his or her remaining Copyright and Related
Rights in the Work or (ii) assert any associated claims and causes of
action with respect to the Work, in either case contrary to Affirmer's
express Statement of Purpose.

4. Limitations and Disclaimers.

 a. No trademark or patent rights held by Affirmer are waived, abandoned,
    surrendered, licensed or otherwise affected by this document.
 b. Affirmer offers the Work as-is and makes no representations or
    warranties of any kind concerning the Work, express, implied,
    statutory or otherwise, including without limitation warranties of
    title, merchantability, fitness for a particular purpose, non
    infringement, or the absence of latent or other defects, accuracy, or
    the present or absence of errors, whether or not discoverable, all to
    the greatest extent permissible under applicable law.
 c. Affirmer disclaims responsibility for clearing rights of other persons
    that may apply to the Work or any use thereof, including without
    limitation any person's Copyright and Related Rights in the Work.
    Further, Affirmer disclaims responsibility for obtaining any necessary
    consents, permissions or other rights required for any use of the
    Work.
 d. Affirmer understands and acknowledges that Creative Commons is not a
    party to this document and has no duty or obligation with respect to
    this CC0 or use of the Work.
````

## File: meta/main.yml
````yaml
---

galaxy_info:
  author: mash
  company: mash
  role_name: jitsi
  namespace: mash
  description: Jitsi
  platforms:
    - name: Debian
      versions:
        - all
    - name: Ubuntu
      versions:
        - all
    - name: Archlinux
      versions:
        - all
    - name: EL
      versions:
        - 7
  license: GPL-3.0-or-later
  min_ansible_version: '2.1'
````

## File: tasks/util/prosody_post_setup_hooks/setup_jitsi_auth_internal.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---
#
# Tasks related to configuring Jitsi internal authentication on a running prosody instance.
#

- name: Ensure Jitsi internal authentication users are configured
  ansible.builtin.command: "{{ devture_systemd_docker_base_host_command_docker }} exec {{ jitsi_identifier }}-prosody prosodyctl --config /config/prosody.cfg.lua register {{ item.username | quote }} meet.jitsi {{ item.password | quote }}"
  with_items: "{{ jitsi_prosody_auth_internal_accounts }}"
  when:
    - jitsi_prosody_auth_internal_accounts|length > 0
  register: jitsi_user_configuration_result
  changed_when: jitsi_user_configuration_result.rc == 0
  no_log: true

#
# Tasks related to clean up after configuring internal authentication.
#
````

## File: tasks/util/setup_jitsi_auth_uvs_install.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Determine jitsi_user_username if undefined
  when: jitsi_user_username == ''
  block:
    - name: Look up Jitsi username by uid
      ansible.builtin.getent:
        database: passwd
        key: "{{ jitsi_uid }}"
      register: result_jitsi_user_username

    - name: Set jitsi_user_username based on uid lookup data
      set_fact:
        jitsi_user_username: "{{ result_jitsi_user_username.ansible_facts.getent_passwd.keys() | list | first }}"

- name: Checkout Prosody Auth Matrix User Verification Plugin Repo
  ansible.builtin.git:
    repo: "{{ jitsi_prosody_auth_matrix_user_verification_repo_location }}"
    dest: "{{ jitsi_prosody_auth_matrix_user_verification_repo_target }}"
    version: "{{ jitsi_prosody_auth_matrix_user_verification_repo_version }}"
  become: true
  become_user: "{{ jitsi_user_username }}"

- name: Install Prosody Auth Matrix User Verification Plugin
  ansible.builtin.copy:
    remote_src: true
    src: "{{ jitsi_prosody_auth_matrix_user_verification_repo_target }}/{{ item.path }}"
    dest: "{{ jitsi_prosody_plugins_path }}/{{ item.path }}"
    mode: "0644"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items: "{{ jitsi_prosody_auth_matrix_files }}"
  when: item.when | bool
````

## File: tasks/util/setup_jitsi_auth_uvs_uninstall.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
# SPDX-FileCopyrightText: 2024 Kim Brose
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Remove all files regarding prosody mod auth_matrix_user_verification
  ansible.builtin.file:
    path: "{{ item }}"
    state: absent
  loop:
    - "{{ jitsi_prosody_auth_matrix_user_verification_repo_target | flatten  }}"
    - "{{ jitsi_prosody_auth_matrix_files | map(attribute='path') | map('regex_replace', '^', jitsi_prosody_plugins_path+'/') | list | flatten }}"
  register: jitsi_prosody_auth_matrix_user_verification_uninstalled

- when: jitsi_prosody_auth_matrix_user_verification_uninstalled.changed
  block:
    - name: Populate service facts
      ansible.builtin.service_facts:

    - name: Ensure prosody is restarted later on if currently running
      ansible.builtin.set_fact:
        jitsi_prosody_require_restart: "{{ true if ansible_facts.services[jitsi_identifier + '-prosody.service']['state'] == 'running' else false }}"
````

## File: tasks/util/setup_jitsi_prosody_post_setup_hooks.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
# SPDX-FileCopyrightText: 2023 Nikita Chernyi
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---
#####
#
# This tasks file starts and stops (if state before was stopped) a prosody container during setup to run commands,
# that require a running prosody container.
# The task is called in ../setup_jitsi_prosody_install.yml.
#
# Important: The task is called conditionally, as to only start if really needed.
# So if you add or change anything - remember to also change the 'when' in:  ../setup_jitsi_prosody_install.yml
#
#####

#
# Start Necessary Services
#

- name: Ensure jitsi-prosody container is restarted
  ansible.builtin.service:
    name: "{{ jitsi_identifier }}-prosody"
    state: restarted
    daemon_reload: true
  register: restart_result

- name: Wait a while, so that the prosody instance can manage to start
  ansible.builtin.pause:
    seconds: 7
  when: "restart_result.changed"

- name: Ensure internal authentication is properly configured
  ansible.builtin.include_tasks:
    file: "{{ role_path }}/tasks/util/prosody_post_setup_hooks/setup_jitsi_auth_internal.yml"
  when: jitsi_enable_auth | bool and jitsi_auth_type == "internal"
````

## File: tasks/init_additional_jvb.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- ansible.builtin.set_fact:
    devture_systemd_service_manager_services_list_auto: "{{ [{'name': (jitsi_identifier + '-jvb.service'), 'priority': 1000, 'groups': ['jitsi', 'jitsi-jvb']}] }}"
  when: jitsi_enabled | bool
````

## File: tasks/jicofo_install.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Ensure jitsi-jicofo path exists
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: directory
    mode: "0777"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items:
    - {path: "{{ jitsi_jicofo_base_path }}", when: true}
    - {path: "{{ jitsi_jicofo_config_path }}", when: true}
  when: item.when | bool

- name: Ensure jitsi-jicofo Docker image is pulled
  community.docker.docker_image:
    name: "{{ jitsi_jicofo_container_image }}"
    source: "{{ 'pull' if ansible_version.major > 2 or ansible_version.minor > 7 else omit }}"
    force_source: "{{ jitsi_jicofo_container_image_force_pull if ansible_version.major > 2 or ansible_version.minor >= 8 else omit }}"
    force: "{{ omit if ansible_version.major > 2 or ansible_version.minor >= 8 else jitsi_jicofo_container_image_force_pull }}"
  register: result
  retries: "{{ devture_playbook_help_container_retries_count }}"
  delay: "{{ devture_playbook_help_container_retries_delay }}"
  until: result is not failed

- name: Ensure jitsi-jicofo environment variables file created
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jicofo/env.j2"
    dest: "{{ jitsi_jicofo_base_path }}/env"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0640"
  when: jitsi_enabled | bool

- name: Ensure jitsi-jicofo configuration files created
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jicofo/{{ item }}.j2"
    dest: "{{ jitsi_jicofo_config_path }}/{{ item }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0644"
  with_items:
    - sip-communicator.properties
    - logging.properties

- name: Ensure jitsi-jicofo systemd service installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jicofo/jicofo.service.j2"
    dest: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jicofo.service"
    mode: "0644"
````

## File: tasks/jicofo_uninstall.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Check existence of jitsi-jicofo systemd service
  ansible.builtin.stat:
    path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jicofo.service"
  register: jitsi_jicofo_service_stat

- when: jitsi_jicofo_service_stat.stat.exists | bool
  block:
    - name: Ensure jitsi-jicofo is stopped
      ansible.builtin.service:
        name: "{{ jitsi_identifier }}-jicofo"
        state: stopped
        enabled: false
        daemon_reload: true

    - name: Ensure jitsi-jicofo systemd service doesn't exist
      ansible.builtin.file:
        path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jicofo.service"
        state: absent

    - name: Ensure jitsi-jicofo paths doesn't exist
      ansible.builtin.file:
        path: "{{ jitsi_jicofo_base_path }}"
        state: absent
````

## File: tasks/jvb_install.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Ensure jitsi-jvb path exists
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: directory
    mode: "0777"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items:
    - {path: "{{ jitsi_jvb_base_path }}", when: true}
    - {path: "{{ jitsi_jvb_config_path }}", when: true}
  when: item.when | bool

- name: Ensure jitsi-jvb container image is pulled
  community.docker.docker_image:
    name: "{{ jitsi_jvb_container_image }}"
    source: "{{ 'pull' if ansible_version.major > 2 or ansible_version.minor > 7 else omit }}"
    force_source: "{{ jitsi_jvb_container_image_force_pull if ansible_version.major > 2 or ansible_version.minor >= 8 else omit }}"
    force: "{{ omit if ansible_version.major > 2 or ansible_version.minor >= 8 else jitsi_jvb_container_image_force_pull }}"
  register: result
  retries: "{{ devture_playbook_help_container_retries_count }}"
  delay: "{{ devture_playbook_help_container_retries_delay }}"
  until: result is not failed

- name: Ensure jitsi-jvb configuration files created
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jvb/{{ item }}.j2"
    dest: "{{ jitsi_jvb_config_path }}/{{ item }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0644"
  with_items:
    - custom-sip-communicator.properties
    - logging.properties

- name: Ensure jitsi-jvb support files installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jvb/{{ item }}.j2"
    dest: "{{ jitsi_jvb_base_path }}/{{ item }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0640"
  with_items:
    - env
    - labels

- name: Ensure jitsi-jvb systemd service installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/jvb/jvb.service.j2"
    dest: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jvb.service"
    mode: "0644"
````

## File: tasks/jvb_uninstall.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Check existence of jitsi-jvb service
  ansible.builtin.stat:
    path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jvb.service"
  register: jitsi_jvb_service_stat

- when: jitsi_jvb_service_stat.stat.exists | bool
  block:
    - name: Ensure jitsi-jvb systemd service is stopped
      ansible.builtin.service:
        name: "{{ jitsi_identifier }}-jvb"
        state: stopped
        enabled: false
        daemon_reload: true

    - name: Ensure jitsi-jvb systemd service doesn't exist
      ansible.builtin.file:
        path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-jvb.service"
        state: absent

    - name: Ensure jitsi-jvb paths doesn't exist
      ansible.builtin.file:
        path: "{{ jitsi_jvb_base_path }}"
        state: absent
````

## File: tasks/main.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- tags:
    - setup-additional-jitsi-jvb
    - install-additional-jitsi-jvb
  block:
    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/init_additional_jvb.yml"

- tags:
    - setup-all
    - setup-jitsi
    - setup-additional-jitsi-jvb
    - install-all
    - install-jitsi
    - install-additional-jitsi-jvb
  block:
    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/validate_config.yml"

    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/setup_jitsi_base.yml"

- tags:
    - setup-all
    - setup-jitsi
    - install-all
    - install-jitsi
  block:
    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/web_install.yml"

    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/prosody_install.yml"

    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/jicofo_install.yml"

- tags:
    - setup-all
    - setup-jitsi
  block:
    - when: not jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/web_uninstall.yml"

    - when: not jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/prosody_uninstall.yml"

    - when: not jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/jicofo_uninstall.yml"

- tags:
    - setup-all
    - setup-jitsi
    - setup-additional-jitsi-jvb
    - install-all
    - install-jitsi
    - install-additional-jitsi-jvb
  block:
    - when: jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/jvb_install.yml"

- tags:
    - setup-all
    - setup-jitsi
    - setup-additional-jitsi-jvb
  block:
    - when: not jitsi_enabled | bool
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/jvb_uninstall.yml"

- tags:
    - self-check
  block:
    - when: jitsi_enable_auth | bool and jitsi_auth_type == 'matrix'
      ansible.builtin.include_tasks: "{{ role_path }}/tasks/self_check_matrix_auth.yml"
````

## File: tasks/prosody_install.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Ensure jitsi-prosody environment exists
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: directory
    mode: "0777"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items:
    - {path: "{{ jitsi_prosody_base_path }}", when: true}
    - {path: "{{ jitsi_prosody_config_path }}", when: true}
    - {path: "{{ jitsi_prosody_plugins_path }}", when: true}
    - {path: "{{ jitsi_prosody_ext_path }}", when: true}
  when: item.when | bool

- name: Ensure jitsi-prosody container image is pulled
  community.docker.docker_image:
    name: "{{ jitsi_prosody_container_image }}"
    source: "{{ 'pull' if ansible_version.major > 2 or ansible_version.minor > 7 else omit }}"
    force_source: "{{ jitsi_prosody_container_image_force_pull if ansible_version.major > 2 or ansible_version.minor >= 8 else omit }}"
    force: "{{ omit if ansible_version.major > 2 or ansible_version.minor >= 8 else jitsi_prosody_container_image_force_pull }}"
  register: result
  retries: "{{ devture_playbook_help_container_retries_count }}"
  delay: "{{ devture_playbook_help_container_retries_delay }}"
  until: result is not failed

- name: Ensure jitsi-prosody support files installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/prosody/{{ item }}.j2"
    dest: "{{ jitsi_prosody_base_path }}/{{ item }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0640"
  with_items:
    - env
    - labels

# Configure matrix authentication.
- name: Install user verification plugin
  ansible.builtin.include_tasks:
    file: "{{ role_path }}/tasks/util/setup_jitsi_auth_uvs_install.yml"
  when: jitsi_enable_auth | bool and jitsi_auth_type == "matrix"

# Remove matrix authentication if disabled
- name: Ensure user verification plugin is not present if matrix auth is disabled
  ansible.builtin.include_tasks:
    file: "{{ role_path }}/tasks/util/setup_jitsi_auth_uvs_uninstall.yml"
  when: (not jitsi_enable_auth | bool) or (jitsi_auth_type != "matrix")

- name: Ensure jitsi-prosody systemd service file is installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/prosody/prosody.service.j2"
    dest: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-prosody.service"
    mode: "0644"
  register: jitsi_prosody_systemd_service_result

# Tasks that require a running prosody container are called in this file.
- name: Run prosody related tasks, that require a running container.
  ansible.builtin.include_tasks:
    file: "{{ role_path }}/tasks/util/setup_jitsi_prosody_post_setup_hooks.yml"
  when:
    - jitsi_enable_auth | bool and jitsi_auth_type == "internal"
````

## File: tasks/prosody_uninstall.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Check if jitsi-prosody systemd service file exists
  ansible.builtin.stat:
    path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-prosody.service"
  register: jitsi_prosody_service_stat

- when: jitsi_prosody_service_stat.stat.exists | bool
  block:
    - name: Ensure jitsi-prosody systemd service is stopped
      ansible.builtin.service:
        name: "{{ jitsi_identifier }}-prosody"
        state: stopped
        enabled: false
        daemon_reload: true

    - name: Ensure jitsi-prosody systemd service file doesn't exist
      ansible.builtin.file:
        path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-prosody.service"
        state: absent

    - name: Ensure jitsi-prosody paths doesn't exist
      ansible.builtin.file:
        path: "{{ jitsi_prosody_base_path }}"
        state: absent
````

## File: tasks/self_check_matrix_auth.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- ansible.builtin.set_fact:
    jitsi_prosody_self_check_uvs_health_url: "{{ jitsi_prosody_auth_matrix_uvs_location }}/health"
    jitsi_element_jitsi_well_known_url: "{{ jitsi_web_public_url }}/.well-known/element/jitsi"

- name: Check if jitsi serves the .well-known/element/jitsi
  ansible.builtin.uri:
    url: "{{ jitsi_element_jitsi_well_known_url }}"
    follow_redirects: none
    return_content: true
    validate_certs: "{{ jitsi_self_check_validate_certificates }}"
    headers:
      Origin: example.com
  check_mode: false
  register: result_well_known_jitsi_element_jitsi
  ignore_errors: true

- name: Fail if .well-known not working
  ansible.builtin.fail:
    msg: |
      Failed checking that the Jitsi well-known file for Element auth is configured at `{{ jitsi_element_jitsi_well_known_url }}`
      Full error: {{ result_well_known_jitsi_element_jitsi }}
  when: "result_well_known_jitsi_element_jitsi.failed"

- name: Parse JSON for well-known payload at the matrix hostname
  ansible.builtin.set_fact:
    well_known_matrix_payload: "{{ result_well_known_jitsi_element_jitsi.content | from_json }}"

- name: Fail if .well-known not CORS-aware
  ansible.builtin.fail:
    msg: "The well-known file on `{{ jitsi_element_jitsi_well_known_url }}` is not CORS-aware. The file needs to be served with an Access-Control-Allow-Origin header set."
  when: "'access_control_allow_origin' not in result_well_known_jitsi_element_jitsi"

- name: Report working .well-known
  ansible.builtin.debug:
    msg: "well-known is configured correctly at `{{ jitsi_element_jitsi_well_known_url }}`"

- name: Check if we can reach the user verification service and if it's healthy
  ansible.builtin.command:
    argv:
      - "docker"
      - "exec"
      - "{{ jitsi_identifier }}-prosody"
      - "wget"
      - "-O"
      - "-"
      - "--quiet"
      - "{{ jitsi_prosody_self_check_uvs_health_url | quote }}"
  register: jitsi_prosody_self_check_uvs_result
  changed_when: false
  ignore_errors: true

- name: Fail if user verification service is not (reachable and healthy)
  ansible.builtin.fail:
    msg: |
      Failed checking user verification service is up (checked endpoint: `{{ jitsi_prosody_self_check_uvs_health_url }}`).
      Full error: {{ jitsi_prosody_self_check_uvs_result }}
  when: "jitsi_prosody_self_check_uvs_result.failed"

- name: Report healthy user verification service
  ansible.builtin.debug:
    msg: "User verification service is working (checked endpoint: `{{ jitsi_prosody_self_check_uvs_health_url }}`)"
````

## File: tasks/setup_jitsi_base.yml
````yaml
# SPDX-FileCopyrightText: 2023 - 2024 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Ensure Jitsi base path exists
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: directory
    mode: "0750"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items:
    - {path: "{{ jitsi_base_path }}", when: true}
  when: item.when | bool

- name: Ensure Jitsi container network is created
  community.general.docker_network:
    enable_ipv6: "{{ devture_systemd_docker_base_ipv6_enabled }}"
    name: "{{ jitsi_container_network }}"
    driver: bridge
    driver_options: "{{ devture_systemd_docker_base_container_networks_driver_options }}"
````

## File: tasks/validate_config.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Fail if required Jitsi settings not defined
  ansible.builtin.fail:
    msg: >
      You need to define a required configuration setting (`{{ item }}`).
  when: "vars[item] == ''"
  with_items:
    - jitsi_identifier
    - jitsi_uid
    - jitsi_gid
    - jitsi_hostname
    - jitsi_path_prefix
    - jitsi_container_network

    - jitsi_architecture

    - jitsi_jibri_xmpp_password
    - jitsi_jicofo_auth_password
    - jitsi_jvb_auth_password

- name: Fail if on an unsupported architecture
  ansible.builtin.fail:
    msg: "Jitsi only supports the amd64 and arm64 architectures right now. See https://github.com/jitsi/docker-jitsi-meet/issues/1069 and https://github.com/jitsi/docker-jitsi-meet/issues/1214"
  when: jitsi_enabled | bool and jitsi_architecture not in ['amd64', 'arm64']

- name: Fail if authentication is enabled, but not properly configured.
  ansible.builtin.fail:
    msg: >-
      You have enabled authentication, but the configured auth type is missing required configuration.

      Auth type 'internal':
      At least one Jitsi user needs to be defined in `jitsi_prosody_auth_internal_accounts` when using internal authentication.
      If you're setting up Jitsi for the first time, you may have missed a step.
      Refer to our setup instructions (docs/configuring-playbook-jitsi.md).

      Auth type 'matrix':
      If you want to enable matrix_user_verification in jitsi,
      please provide an auth token for Matrix User Verification Service (UVS) using `jitsi_prosody_auth_matrix_uvs_auth_token`.
      If the user-verfication-service is also managed by this playbook the token is derived from `matrix_homeserver_generic_secret_key` in the group vars.
  when:
    - jitsi_enable_auth | bool
    - ((jitsi_auth_type == 'internal' and jitsi_prosody_auth_internal_accounts|length == 0)
      or (jitsi_auth_type == 'matrix' and jitsi_prosody_auth_matrix_uvs_auth_token|length == 0))

- name: (Deprecation) Catch and report renamed settings
  ansible.builtin.fail:
    msg: >-
      Your configuration contains a variable, which now has a different name.
      Please change your configuration to rename the variable (`{{ item.old }}` -> `{{ item.new }}`).
  when: "item.old in vars"
  with_items:
    - {'old': 'jitsi_web_config_constraints_enabled', 'new': '<Now unnecessary. Constraints are always applied automatically>'}
    - {'old': 'jitsi_web_config_constraints_video_aspectRatio', 'new': '<Not applicable anymore>'}
    - {'old': 'jitsi_web_config_constraints_video_height_ideal', 'new': 'jitsi_web_config_resolution_height_ideal_and_max'}
    - {'old': 'jitsi_web_config_constraints_video_height_max', 'new': 'jitsi_web_config_resolution_height_ideal_and_max'}
    - {'old': 'jitsi_web_config_constraints_video_height_min', 'new': 'jitsi_web_config_resolution_height_min'}
    - {'old': 'jitsi_web_config_disableAudioLevels', 'new': '<Can be set by using jitsi_web_custom_config_extension. Example in docs/configuring-playbook-jitsi.md>'}
    - {'old': 'jitsi_web_config_enableLayerSuspension', 'new': '<Can be set by using jitsi_web_custom_config_extension. Example in docs/configuring-playbook-jitsi.md>'}
    - {'old': 'jitsi_web_config_channelLastN', 'new': '<Can be set by using jitsi_web_custom_config_extension. Example in docs/configuring-playbook-jitsi.md>'}
    - {'old': 'jitsi_web_config_testing_p2pTestMode', 'new': '<Can be set by using jitsi_web_custom_config_extension>'}
    - {'old': 'jitsi_web_config_start_with_audio_muted', 'new': '<Superseded by jitsi_web_config_start_audio_muted_after_nth_participant>'}
    - {'old': 'jitsi_web_config_start_with_video_muted', 'new': '<Superseded by jitsi_web_config_start_video_muted_after_nth_participant>'}
    - {'old': 'jitsi_web_interface_config_show_watermark_for_guests', 'new': '<Not applicable anymore>'}
    - {'old': 'jitsi_web_interface_config_invitation_powered_by', 'new': '<Not applicable anymore>'}
    - {'old': 'jitsi_web_interface_config_lang_detection', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_show_jitsi_watermark', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_jitsi_watermark_link', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_show_brand_watermark', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_brand_watermark_link', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_generate_room_names_on_welcome_page', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_display_welcome_page_content', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_app_name', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_native_app_name', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_provider_name', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_show_powered_by', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_disable_transcription_subtitles', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
    - {'old': 'jitsi_web_interface_config_show_deep_linking_image', 'new': '<Deprecated, use jitsi_web_custom_interface_config_extension instead'}
````

## File: tasks/web_install.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Ensure jitsi-web path exists
  ansible.builtin.file:
    path: "{{ item.path }}"
    state: directory
    mode: "0777"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
  with_items:
    - {path: "{{ jitsi_web_base_path }}", when: true}
    - {path: "{{ jitsi_web_config_path }}", when: true}
    - {path: "{{ jitsi_web_transcripts_path }}", when: true}
    - {path: "{{ jitsi_web_crontabs_path }}", when: true}
    - {path: "{{ jitsi_web_well_known_path }}", when: "{{ jitsi_web_well_known_enabled }}"}
    - {path: "{{ jitsi_web_well_known_path }}/element", when: "{{ jitsi_web_well_known_element_jitsi_enabled }}"}
  when: item.when | bool

- name: Ensure jitsi-web support files installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/web/{{ item.file }}.j2"
    dest: "{{ jitsi_web_base_path }}/{{ item.file }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "{{ item.mode }}"
  with_items:
    - file: env
      mode: 640
    - file: labels
      mode: 640
    - file: base.html
      mode: 644

- name: Ensure jitsi-web configuration files created
  ansible.builtin.template:
    src: "{{ role_path }}/templates/web/{{ item }}.j2"
    dest: "{{ jitsi_web_config_path }}/{{ item }}"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"
    mode: "0644"
  with_items:
    - custom-config.js
    - custom-interface_config.js

- name: Ensure Jitsi /.well-known/element/jitsi installed
  when: jitsi_web_well_known_element_jitsi_enabled | bool
  ansible.builtin.copy:
    content: "{{ jitsi_web_well_known_element_jitsi_json }}"
    dest: "{{ jitsi_web_well_known_path }}/element/jitsi"
    mode: "0644"
    owner: "{{ jitsi_uid }}"
    group: "{{ jitsi_gid }}"

- name: Ensure Jitsi /.well-known/element/jitsi removed
  when: not jitsi_web_well_known_element_jitsi_enabled | bool
  ansible.builtin.file:
    path: "{{ jitsi_web_well_known_path }}/element/jitsi"
    state: absent

- name: Ensure jitsi-web container image is pulled
  community.docker.docker_image:
    name: "{{ jitsi_web_container_image }}"
    source: "{{ 'pull' if ansible_version.major > 2 or ansible_version.minor > 7 else omit }}"
    force_source: "{{ jitsi_web_container_image_force_pull if ansible_version.major > 2 or ansible_version.minor >= 8 else omit }}"
    force: "{{ omit if ansible_version.major > 2 or ansible_version.minor >= 8 else jitsi_web_container_image_force_pull }}"
  register: result
  retries: "{{ devture_playbook_help_container_retries_count }}"
  delay: "{{ devture_playbook_help_container_retries_delay }}"
  until: result is not failed

- name: Ensure jitsi-web systemd service installed
  ansible.builtin.template:
    src: "{{ role_path }}/templates/web/web.service.j2"
    dest: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-web.service"
    mode: "0644"
````

## File: tasks/web_uninstall.yml
````yaml
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

---

- name: Check existence of jitsi-web systemd service
  ansible.builtin.stat:
    path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-web.service"
  register: jitsi_web_service_stat

- when: jitsi_web_service_stat.stat.exists | bool
  block:
    - name: Ensure jitsi-web systemd serviec is stopped
      ansible.builtin.service:
        name: "{{ jitsi_identifier }}-web"
        state: stopped
        enabled: false
        daemon_reload: true

    - name: Ensure jitsi-web systemd service doesn't exist
      ansible.builtin.file:
        path: "{{ devture_systemd_docker_base_systemd_path }}/{{ jitsi_identifier }}-web.service"
        state: absent

    - name: Ensure jitsi-web paths doesn't exist
      ansible.builtin.file:
        path: "{{ jitsi_web_base_path }}"
        state: absent
````

## File: templates/jicofo/env.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

AUTH_TYPE={{ jitsi_auth_type }}
BRIDGE_AVG_PARTICIPANT_STRESS
BRIDGE_STRESS_THRESHOLD
ENABLE_AUTH={{ 1 if jitsi_enable_auth else 0 }}
ENABLE_AUTO_OWNER
ENABLE_CODEC_VP8
ENABLE_CODEC_VP9
ENABLE_CODEC_H264
ENABLE_OCTO
ENABLE_RECORDING
ENABLE_SCTP
ENABLE_AUTO_LOGIN
JICOFO_AUTH_USER={{ jitsi_jicofo_auth_user }}
JICOFO_AUTH_PASSWORD={{ jitsi_jicofo_auth_password }}
JICOFO_ENABLE_BRIDGE_HEALTH_CHECKS
JICOFO_CONF_INITIAL_PARTICIPANT_WAIT_TIMEOUT
JICOFO_CONF_SINGLE_PARTICIPANT_TIMEOUT
JICOFO_ENABLE_HEALTH_CHECKS
JICOFO_SHORT_ID
JICOFO_RESERVATION_ENABLED
JICOFO_RESERVATION_REST_BASE_URL
JIBRI_BREWERY_MUC={{ jitsi_jibri_brewery_muc }}
JIBRI_REQUEST_RETRIES
JIBRI_PENDING_TIMEOUT={{ jitsi_jibri_pending_timeout }}
JIGASI_BREWERY_MUC
JIGASI_SIP_URI
JVB_BREWERY_MUC={{ jitsi_jvb_brewery_muc }}
MAX_BRIDGE_PARTICIPANTS
OCTO_BRIDGE_SELECTION_STRATEGY
SENTRY_DSN={{ jitsi_jicofo_sentry_dsn }}
SENTRY_ENVIRONMENT
SENTRY_RELEASE
TZ={{ jitsi_timezone }}
XMPP_DOMAIN={{ jitsi_xmpp_domain }}
XMPP_AUTH_DOMAIN={{ jitsi_xmpp_auth_domain }}
XMPP_INTERNAL_MUC_DOMAIN={{ jitsi_xmpp_internal_muc_domain }}
XMPP_MUC_DOMAIN={{ jitsi_xmpp_muc_domain }}
XMPP_SERVER={{ jitsi_xmpp_server }}
````

## File: templates/jicofo/jicofo.service.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

#jinja2: lstrip_blocks: "True"
[Unit]
Description=Jitsi Jicofo server ({{ jitsi_identifier }})
{% for service in jitsi_jicofo_systemd_required_services_list %}
Requires={{ service }}
After={{ service }}
{% endfor %}
DefaultDependencies=no

[Service]
Type=simple
Environment="HOME={{ devture_systemd_docker_base_systemd_unit_home_path }}"
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-jicofo 2>/dev/null || true'
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-jicofo 2>/dev/null || true'

ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} create \
			--rm \
			--name={{ jitsi_identifier }}-jicofo \
			--log-driver=none \
			--network={{ jitsi_container_network }} \
			--env-file={{ jitsi_jicofo_base_path }}/env \
			--mount type=bind,src={{ jitsi_jicofo_config_path }},dst=/config \
			{% for arg in jitsi_jicofo_container_extra_arguments %}
			{{ arg }} \
			{% endfor %}
			{{ jitsi_jicofo_container_image }}

{% for network in jitsi_jicofo_container_additional_networks %}
ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} network connect {{ network }} {{ jitsi_identifier }}-jicofo
{% endfor %}

ExecStart={{ devture_systemd_docker_base_host_command_docker }} start --attach {{ jitsi_identifier }}-jicofo

ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-jicofo 2>/dev/null || true'
ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-jicofo 2>/dev/null || true'
Restart=always
RestartSec=30
SyslogIdentifier={{ jitsi_identifier }}-jicofo

[Install]
WantedBy=multi-user.target
````

## File: templates/jicofo/logging.properties.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{% raw %}
{{ if .Env.SENTRY_DSN | default "0" | toBool }}
handlers=java.util.logging.ConsoleHandler,io.sentry.jul.SentryHandler
{{ else }}
handlers= java.util.logging.ConsoleHandler
{{ end }}
{% endraw %}

java.util.logging.ConsoleHandler.level = ALL
java.util.logging.ConsoleHandler.formatter = net.java.sip.communicator.util.ScLogFormatter

net.java.sip.communicator.util.ScLogFormatter.programname=Jicofo

.level=INFO
net.sf.level=SEVERE
net.java.sip.communicator.plugin.reconnectplugin.level=FINE
org.ice4j.level=SEVERE
org.jitsi.impl.neomedia.level=SEVERE
io.sentry.jul.SentryHandler.level=WARNING

# Do not worry about missing strings
net.java.sip.communicator.service.resources.AbstractResourcesService.level=SEVERE

#net.java.sip.communicator.service.protocol.level=ALL

# Enable debug packets logging
#org.jitsi.impl.protocol.xmpp.level=FINE
````

## File: templates/jicofo/sip-communicator.properties.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

org.jitsi.jicofo.ALWAYS_TRUST_MODE_ENABLED=true
org.jitsi.jicofo.BRIDGE_MUC={{ jitsi_jvb_brewery_muc }}@{{ jitsi_xmpp_internal_muc_domain }}

org.jitsi.jicofo.jibri.BREWERY={{ jitsi_jibri_brewery_muc }}@{{ jitsi_xmpp_internal_muc_domain }}
org.jitsi.jicofo.jibri.PENDING_TIMEOUT=90

{% if jitsi_enable_auth %}
org.jitsi.jicofo.auth.URL=XMPP:{{ jitsi_xmpp_domain }}
{% endif %}
````

## File: templates/jvb/custom-sip-communicator.properties.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

org.jitsi.videobridge.xmpp.user.shard.DISABLE_CERTIFICATE_VERIFICATION=true

org.jitsi.videobridge.ENABLE_STATISTICS=true
org.jitsi.videobridge.STATISTICS_TRANSPORT=muc
org.jitsi.videobridge.STATISTICS_INTERVAL=5000

{{ jitsi_jvb_custom_config_extension }}
````

## File: templates/jvb/env.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

DOCKER_HOST_ADDRESS
ENABLE_COLIBRI_WEBSOCKET
ENABLE_OCTO
JVB_AUTH_USER={{ jitsi_jvb_auth_user }}
JVB_AUTH_PASSWORD={{ jitsi_jvb_auth_password }}
JVB_BREWERY_MUC={{ jitsi_jvb_brewery_muc }}
JVB_PORT={{ jitsi_jvb_rtp_udp_port }}
JVB_TCP_HARVESTER_DISABLED=true
JVB_TCP_PORT={{ jitsi_jvb_rtp_tcp_port }}
JVB_TCP_MAPPED_PORT={{ jitsi_jvb_rtp_tcp_port }}
{% if jitsi_jvb_stun_servers|length > 0 %}
JVB_STUN_SERVERS={{ jitsi_jvb_stun_servers|join(',') }}
{% endif %}
JVB_OCTO_BIND_ADDRESS
JVB_OCTO_PUBLIC_ADDRESS
JVB_OCTO_BIND_PORT
JVB_OCTO_REGION
JVB_WS_DOMAIN
JVB_WS_SERVER_ID={{ jitsi_jvb_server_id }}
PUBLIC_URL={{ jitsi_web_public_url }}
SENTRY_DSN={{ jitsi_jvb_sentry_dsn }}
SENTRY_ENVIRONMENT
SENTRY_RELEASE
COLIBRI_REST_ENABLED
SHUTDOWN_REST_ENABLED
TZ={{ jitsi_timezone }}
XMPP_AUTH_DOMAIN={{ jitsi_xmpp_auth_domain }}
XMPP_INTERNAL_MUC_DOMAIN={{ jitsi_xmpp_internal_muc_domain }}
XMPP_SERVER={{ jitsi_xmpp_server }}

{{ jitsi_jvb_environment_variables_extension }}
````

## File: templates/jvb/jvb.service.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

#jinja2: lstrip_blocks: "True"
[Unit]
Description=Jitsi Video Bridge ({{ jitsi_identifier }})
{% for service in jitsi_jvb_systemd_required_services_list %}
Requires={{ service }}
After={{ service }}
{% endfor %}
DefaultDependencies=no

[Service]
Type=simple
Environment="HOME={{ devture_systemd_docker_base_systemd_unit_home_path }}"
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-jvb 2>/dev/null || true'
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-jvb 2>/dev/null || true'

ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} create \
			--rm \
			--name={{ jitsi_identifier }}-jvb \
			--log-driver=none \
			--network={{ jitsi_container_network }} \
			--network-alias=jvb.meet.jitsi \
			--env-file={{ jitsi_jvb_base_path }}/env \
			--label-file={{ jitsi_jvb_base_path }}/labels \
			{% if jitsi_jvb_container_rtp_udp_host_bind_port %}
			-p {{ jitsi_jvb_container_rtp_udp_host_bind_port }}:{{ jitsi_jvb_rtp_udp_port }}/udp \
			{% endif %}
			{% if jitsi_jvb_container_rtp_tcp_host_bind_port %}
			-p {{ jitsi_jvb_container_rtp_tcp_host_bind_port }}:{{ jitsi_jvb_rtp_tcp_port }} \
			{% endif %}
			{% if jitsi_jvb_container_colibri_ws_host_bind_port %}
			-p {{ jitsi_jvb_container_colibri_ws_host_bind_port }}:9090 \
			{% endif %}
			--mount type=bind,src={{ jitsi_jvb_config_path }},dst=/config \
			{% for arg in jitsi_jvb_container_extra_arguments %}
			{{ arg }} \
			{% endfor %}
			{{ jitsi_jvb_container_image }}

{% for network in jitsi_jvb_container_additional_networks %}
ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} network connect {{ network }} {{ jitsi_identifier }}-jvb
{% endfor %}

ExecStart={{ devture_systemd_docker_base_host_command_docker }} start --attach {{ jitsi_identifier }}-jvb

ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-jvb 2>/dev/null || true'
ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-jvb 2>/dev/null || true'
Restart=always
RestartSec=30
SyslogIdentifier={{ jitsi_identifier }}-jvb

[Install]
WantedBy=multi-user.target
````

## File: templates/jvb/labels.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{% if jitsi_jvb_container_labels_traefik_enabled %}
traefik.enable=true

{% if jitsi_jvb_container_labels_traefik_docker_network %}
traefik.docker.network={{ jitsi_jvb_container_labels_traefik_docker_network }}
{% endif %}

{% set middlewares = [] %}

{% if jitsi_jvb_container_labels_traefik_path_prefix != '/' %}
traefik.http.middlewares.{{ jitsi_identifier }}-jvb-strip-prefix.stripprefix.prefixes={{ jitsi_jvb_container_labels_traefik_path_prefix }}
{% set middlewares = middlewares + [jitsi_identifier + '-jvb-strip-prefix'] %}
{% endif %}

{% if jitsi_jvb_container_labels_traefik_additional_request_headers.keys() | length > 0 %}
{% for name, value in jitsi_jvb_container_labels_traefik_additional_request_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-jvb-add-request-headers.headers.customrequestheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-jvb-add-request-headers'] %}
{% endif %}

{% if jitsi_jvb_container_labels_traefik_additional_response_headers.keys() | length > 0 %}
{% for name, value in jitsi_jvb_container_labels_traefik_additional_response_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-jvb-add-response-headers.headers.customresponseheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-jvb-add-response-headers'] %}
{% endif %}

traefik.http.routers.{{ jitsi_identifier }}-jvb.rule={{ jitsi_jvb_container_labels_traefik_rule }}
{% if jitsi_jvb_container_labels_traefik_priority | int > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-jvb.priority={{ jitsi_jvb_container_labels_traefik_priority }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-jvb.service={{ jitsi_identifier }}-jvb
{% if middlewares | length > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-jvb.middlewares={{ middlewares | join(',') }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-jvb.entrypoints={{ jitsi_jvb_container_labels_traefik_entrypoints }}
traefik.http.routers.{{ jitsi_identifier }}-jvb.tls={{ jitsi_jvb_container_labels_traefik_tls | to_json }}
{% if jitsi_jvb_container_labels_traefik_tls %}
traefik.http.routers.{{ jitsi_identifier }}-jvb.tls.certResolver={{ jitsi_jvb_container_labels_traefik_tls_certResolver }}
{% endif %}

traefik.http.services.{{ jitsi_identifier }}-jvb.loadbalancer.server.port=9090
{% endif %}

{{ jitsi_jvb_container_labels_additional_labels }}
````

## File: templates/jvb/logging.properties.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{% raw %}
{{ if .Env.SENTRY_DSN | default "0" | toBool }}
handlers=java.util.logging.ConsoleHandler,io.sentry.jul.SentryHandler
{{ else }}
handlers= java.util.logging.ConsoleHandler
{{ end }}
{% endraw %}

java.util.logging.ConsoleHandler.level = ALL
java.util.logging.ConsoleHandler.formatter = net.java.sip.communicator.util.ScLogFormatter

net.java.sip.communicator.util.ScLogFormatter.programname=JVB

.level=INFO

org.jitsi.videobridge.xmpp.ComponentImpl.level=FINE
io.sentry.jul.SentryHandler.level=WARNING

# All of the INFO level logs from MediaStreamImpl are unnecessary in the context of jitsi-videobridge.
org.jitsi.impl.neomedia.MediaStreamImpl.level=WARNING
````

## File: templates/prosody/env.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev
SPDX-FileCopyrightText: 2023 Nikita Chernyi

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{#
  See https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/#configuration
  for details about how these environment variables work.
#}

AUTH_TYPE={{ jitsi_auth_type }}
DISABLE_POLLS
ENABLE_AUTH={{ 1 if jitsi_enable_auth else 0 }}
ENABLE_AV_MODERATION={{1 if jitsi_enable_av_moderation else 0}}
ENABLE_BREAKOUT_ROOMS={{1 if jitsi_enable_breakout_rooms else 0}}
ENABLE_GUESTS={{ 1 if jitsi_enable_guests else 0 }}
ENABLE_LOBBY={{ 1 if jitsi_enable_lobby else 0 }}
ENABLE_XMPP_WEBSOCKET
GLOBAL_CONFIG
GLOBAL_MODULES
JIBRI_RECORDER_USER={{ jitsi_jibri_recorder_user }}
JIBRI_RECORDER_PASSWORD={{ jitsi_jibri_recorder_password }}
JIBRI_XMPP_USER={{ jitsi_jibri_xmpp_user }}
JIBRI_XMPP_PASSWORD={{ jitsi_jibri_xmpp_password }}
JICOFO_AUTH_USER={{ jitsi_jicofo_auth_user }}
JICOFO_AUTH_PASSWORD={{ jitsi_jicofo_auth_password }}
JICOFO_COMPONENT_SECRET
JIGASI_XMPP_USER={{ jitsi_jigasi_xmpp_user }}
JIGASI_XMPP_PASSWORD={{ jitsi_jigasi_xmpp_password }}
JVB_AUTH_USER={{ jitsi_jvb_auth_user }}
JVB_AUTH_PASSWORD={{ jitsi_jvb_auth_password }}
JWT_APP_ID
JWT_APP_SECRET
JWT_ACCEPTED_ISSUERS
JWT_ACCEPTED_AUDIENCES
JWT_ASAP_KEYSERVER
JWT_ALLOW_EMPTY
JWT_AUTH_TYPE
JWT_TOKEN_AUTH_MODULE
LOG_LEVEL
LDAP_AUTH_METHOD={{ jitsi_ldap_auth_method }}
LDAP_BASE={{ jitsi_ldap_base }}
LDAP_BINDDN={{ jitsi_ldap_binddn }}
LDAP_BINDPW={{ jitsi_ldap_bindpw }}
LDAP_FILTER={{ jitsi_ldap_filter }}
LDAP_VERSION={{ jitsi_ldap_version }}
LDAP_TLS_CIPHERS={{ jitsi_ldap_tls_ciphers }}
LDAP_TLS_CHECK_PEER={{ 1 if jitsi_ldap_tls_check_peer else 0 }}
LDAP_TLS_CACERT_FILE={{ jitsi_ldap_tls_cacert_file }}
LDAP_TLS_CACERT_DIR={{ jitsi_ldap_tls_cacert_dir }}
LDAP_START_TLS={{ 1 if jitsi_ldap_start_tls else 0 }}
LDAP_URL={{ jitsi_ldap_url }}
LDAP_USE_TLS={{ 1 if jitsi_ldap_use_tls else 0 }}
{% if jitsi_auth_type == 'matrix' %}
MATRIX_UVS_ISSUER={{ jitsi_prosody_auth_matrix_jwt_app_id }}
MATRIX_UVS_URL={{ jitsi_prosody_auth_matrix_uvs_location }}
{% if jitsi_prosody_auth_matrix_uvs_auth_token is defined %}
MATRIX_UVS_AUTH_TOKEN={{ jitsi_prosody_auth_matrix_uvs_auth_token }}
{% endif %}
MATRIX_UVS_SYNC_POWER_LEVELS={{ 'true' if jitsi_prosody_auth_matrix_uvs_sync_power_levels else 'false' }}
{% endif %}
PUBLIC_URL={{ jitsi_web_public_url }}
TURN_CREDENTIALS={{ jitsi_turn_credentials }}
TURN_HOST={{ jitsi_turn_host }}
TURNS_HOST={{ jitsi_turns_host }}
TURN_PORT={{ jitsi_turn_port }}
TURNS_PORT={{ jitsi_turns_port }}
TURN_TRANSPORT={{ jitsi_turn_transport }}
TZ={{ jitsi_timezone }}
XMPP_DOMAIN={{ jitsi_xmpp_domain }}
XMPP_AUTH_DOMAIN={{ jitsi_xmpp_auth_domain }}
XMPP_GUEST_DOMAIN={{ jitsi_xmpp_guest_domain }}
XMPP_MUC_DOMAIN={{ jitsi_xmpp_muc_domain }}
XMPP_INTERNAL_MUC_DOMAIN={{ jitsi_xmpp_internal_muc_domain }}
XMPP_MODULES={{ jitsi_xmpp_modules }}
XMPP_MUC_MODULES={{ jitsi_muc_modules | join(',') }}
XMPP_INTERNAL_MUC_MODULES=
XMPP_RECORDER_DOMAIN={{ jitsi_recorder_domain }}
XMPP_CROSS_DOMAIN=true
{% if jitsi_prosody_max_participants is number %}
MAX_PARTICIPANTS={{ jitsi_prosody_max_participants }}
{% endif %}
````

## File: templates/prosody/labels.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{% if jitsi_prosody_container_labels_traefik_enabled %}
traefik.enable=true

{% if jitsi_prosody_container_labels_traefik_docker_network %}
traefik.docker.network={{ jitsi_prosody_container_labels_traefik_docker_network }}
{% endif %}

{% set middlewares = [] %}

{% if jitsi_prosody_container_labels_traefik_path_prefix != '/' %}
traefik.http.middlewares.{{ jitsi_identifier }}-prosody-strip-prefix.stripprefix.prefixes={{ jitsi_prosody_container_labels_traefik_path_prefix }}
{% set middlewares = middlewares + [jitsi_identifier + '-prosody-strip-prefix'] %}
{% endif %}

{% if jitsi_prosody_container_labels_traefik_additional_request_headers.keys() | length > 0 %}
{% for name, value in jitsi_prosody_container_labels_traefik_additional_request_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-prosody-add-request-headers.headers.customrequestheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-prosody-add-request-headers'] %}
{% endif %}

{% if jitsi_prosody_container_labels_traefik_additional_response_headers.keys() | length > 0 %}
{% for name, value in jitsi_prosody_container_labels_traefik_additional_response_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-prosody-add-response-headers.headers.customresponseheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-prosody-add-response-headers'] %}
{% endif %}

traefik.http.routers.{{ jitsi_identifier }}-prosody.rule={{ jitsi_prosody_container_labels_traefik_rule }}
{% if jitsi_prosody_container_labels_traefik_priority | int > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-prosody.priority={{ jitsi_prosody_container_labels_traefik_priority }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-prosody.service={{ jitsi_identifier }}-prosody
{% if middlewares | length > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-prosody.middlewares={{ middlewares | join(',') }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-prosody.entrypoints={{ jitsi_prosody_container_labels_traefik_entrypoints }}
traefik.http.routers.{{ jitsi_identifier }}-prosody.tls={{ jitsi_prosody_container_labels_traefik_tls | to_json }}
{% if jitsi_prosody_container_labels_traefik_tls %}
traefik.http.routers.{{ jitsi_identifier }}-prosody.tls.certResolver={{ jitsi_prosody_container_labels_traefik_tls_certResolver }}
{% endif %}

traefik.http.services.{{ jitsi_identifier }}-prosody.loadbalancer.server.port=5280
{% endif %}

{{ jitsi_prosody_container_labels_additional_labels }}
````

## File: templates/prosody/prosody.service.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

#jinja2: lstrip_blocks: "True"
[Unit]
Description=Jitsi Prosody server ({{ jitsi_identifier }})
{% for service in jitsi_prosody_systemd_required_services_list %}
Requires={{ service }}
After={{ service }}
{% endfor %}
DefaultDependencies=no

[Service]
Type=simple
Environment="HOME={{ devture_systemd_docker_base_systemd_unit_home_path }}"
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-prosody 2>/dev/null || true'
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-prosody 2>/dev/null || true'

ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} create \
			--rm \
			--name={{ jitsi_identifier }}-prosody \
			--log-driver=none \
			--network={{ jitsi_container_network }} \
			--network-alias={{ jitsi_xmpp_server }} \
			{% if jitsi_prosody_container_http_host_bind_port %}
			-p {{ jitsi_prosody_container_http_host_bind_port }}:5280 \
			{% endif %}
			{% if jitsi_prosody_container_jvb_host_bind_port %}
			-p {{ jitsi_prosody_container_jvb_host_bind_port }}:5222 \
			{% endif %}
			--env-file={{ jitsi_prosody_base_path }}/env \
			--label-file={{ jitsi_prosody_base_path }}/labels \
			--mount type=bind,src={{ jitsi_prosody_config_path }},dst=/config \
			--mount type=bind,src={{ jitsi_prosody_plugins_path }},dst=/prosody-plugins-custom \
			{% for arg in jitsi_prosody_container_extra_arguments %}
			{{ arg }} \
			{% endfor %}
			{{ jitsi_prosody_container_image }}

{% for network in jitsi_prosody_container_additional_networks %}
ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} network connect {{ network }} {{ jitsi_identifier }}-prosody
{% endfor %}

ExecStart={{ devture_systemd_docker_base_host_command_docker }} start --attach {{ jitsi_identifier }}-prosody

ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-prosody 2>/dev/null || true'
ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-prosody 2>/dev/null || true'
Restart=always
RestartSec=30
SyslogIdentifier={{ jitsi_identifier }}-prosody

[Install]
WantedBy=multi-user.target
````

## File: templates/web/base.html.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{#
	We override this file (/usr/share/jitsi-meet/base.html) to support serving Jitsi under a subpath.
	See https://github.com/jitsi/jitsi-meet/issues/4917
#}
<base href="{{ jitsi_path_prefix }}{{ '' if jitsi_path_prefix == '/' else '/' }}" />
````

## File: templates/web/custom-config.js.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

config.defaultLanguage = {{ jitsi_web_config_defaultLanguage|to_json }};


if (!config.hasOwnProperty('p2p')) config.p2p = {% raw %}{}{% endraw %};

{% if jitsi_web_stun_servers|length > 0 %}
config.p2p.stunServers = [
	{% for url in jitsi_web_stun_servers %}
		{ urls: {{ url|to_json }} }{% if not loop.last %},{% endif %}
	{% endfor %}
];
{% endif %}

{% if jitsi_disable_gravatar %}
config.gravatar = {'disabled': true};
{% endif %}

{% if jitsi_etherpad_enabled %}
config.etherpad_base = {{ (jitsi_etherpad_base + '/p/') | to_json }}
{% endif %}

{{ jitsi_web_custom_config_extension }}
````

## File: templates/web/custom-interface_config.js.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{{ jitsi_web_custom_interface_config_extension }}
````

## File: templates/web/env.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

AMPLITUDE_ID
ANALYTICS_SCRIPT_URLS
ANALYTICS_WHITELISTED_EVENTS
CALLSTATS_CUSTOM_SCRIPT_URL
CALLSTATS_ID
CALLSTATS_SECRET
CHROME_EXTENSION_BANNER_JSON
CONFCODE_URL
CONFIG_EXTERNAL_CONNECT
DEFAULT_LANGUAGE
DEPLOYMENTINFO_ENVIRONMENT
DEPLOYMENTINFO_ENVIRONMENT_TYPE
DEPLOYMENTINFO_REGION
DEPLOYMENTINFO_SHARD
DEPLOYMENTINFO_USERREGION
DESKTOP_SHARING_FRAMERATE_MIN
DESKTOP_SHARING_FRAMERATE_MAX
DIALIN_NUMBERS_URL
DIALOUT_AUTH_URL
DIALOUT_CODES_URL
DISABLE_AUDIO_LEVELS
DISABLE_DEEP_LINKING
DISABLE_HTTPS=0
DISABLE_POLLS
DISABLE_REACTIONS
DROPBOX_APPKEY
DROPBOX_REDIRECT_URI
DYNAMIC_BRANDING_URL
ENABLE_AUDIO_PROCESSING
ENABLE_AUTH={{ 1 if jitsi_enable_auth else 0 }}
ENABLE_BREAKOUT_ROOMS={{1 if jitsi_enable_breakout_rooms else 0}}
ENABLE_CALENDAR
ENABLE_COLIBRI_WEBSOCKET=0
ENABLE_FILE_RECORDING_SERVICE
ENABLE_FILE_RECORDING_SERVICE_SHARING
ENABLE_FLOC=0
ENABLE_GUESTS={{ 1 if jitsi_enable_guests else 0 }}
ENABLE_HSTS=0
ENABLE_HTTP_REDIRECT=0
ENABLE_IPV6
ENABLE_LETSENCRYPT=0
ENABLE_LIPSYNC
ENABLE_NO_AUDIO_DETECTION
ENABLE_NOISY_MIC_DETECTION
ENABLE_PREJOIN_PAGE
ENABLE_P2P={{ 1 if jitsi_enable_p2p else 0 }}
ENABLE_WELCOME_PAGE
ENABLE_CLOSE_PAGE
ENABLE_RECORDING={{ 1 if jitsi_enable_recording else 0 }}
ENABLE_REMB
ENABLE_REQUIRE_DISPLAY_NAME
ENABLE_SIMULCAST
ENABLE_STATS_ID
ENABLE_STEREO
ENABLE_SUBDOMAINS
ENABLE_TALK_WHILE_MUTED
ENABLE_TCC
ENABLE_TRANSCRIPTIONS={{ 1 if jitsi_enable_transcriptions else 0 }}
ENABLE_XMPP_WEBSOCKET=0
ENABLE_JAAS_COMPONENTS={{ 1 if jitsi_enable_jaas_components else false }}
ETHERPAD_PUBLIC_URL
ETHERPAD_URL_BASE={{ (jitsi_etherpad_base + '/') if jitsi_etherpad_enabled else '' }}
GOOGLE_ANALYTICS_ID
GOOGLE_API_APP_CLIENT_ID
INVITE_SERVICE_URL
JICOFO_AUTH_USER={{ jitsi_jicofo_auth_user }}
LETSENCRYPT_DOMAIN={{ jitsi_letsencrypt_domain }}
LETSENCRYPT_EMAIL={{ jitsi_letsencrypt_email }}
LETSENCRYPT_USE_STAGING=0
MATOMO_ENDPOINT
MATOMO_SITE_ID
MICROSOFT_API_APP_CLIENT_ID
NGINX_RESOLVER
NGINX_WORKER_PROCESSES
NGINX_WORKER_CONNECTIONS
PEOPLE_SEARCH_URL
PUBLIC_URL={{ jitsi_web_public_url }}
RESOLUTION={{ jitsi_web_config_resolution_height_ideal_and_max }}
RESOLUTION_MIN={{ jitsi_web_config_resolution_height_min }}
RESOLUTION_WIDTH={{ jitsi_web_config_resolution_width_ideal_and_max }}
RESOLUTION_WIDTH_MIN={{ jitsi_web_config_resolution_width_min }}
START_AUDIO_MUTED={{ jitsi_web_config_start_audio_muted_after_nth_participant }}
START_AUDIO_ONLY
START_BITRATE
START_SILENT
START_WITH_AUDIO_MUTED
START_VIDEO_MUTED={{ jitsi_web_config_start_video_muted_after_nth_participant }}
START_WITH_VIDEO_MUTED
TESTING_CAP_SCREENSHARE_BITRATE
TESTING_OCTO_PROBABILITY
TOKEN_AUTH_URL
TZ={{ jitsi_timezone }}
VIDEOQUALITY_BITRATE_H264_LOW
VIDEOQUALITY_BITRATE_H264_STANDARD
VIDEOQUALITY_BITRATE_H264_HIGH
VIDEOQUALITY_BITRATE_VP8_LOW
VIDEOQUALITY_BITRATE_VP8_STANDARD
VIDEOQUALITY_BITRATE_VP8_HIGH
VIDEOQUALITY_BITRATE_VP9_LOW
VIDEOQUALITY_BITRATE_VP9_STANDARD
VIDEOQUALITY_BITRATE_VP9_HIGH
VIDEOQUALITY_ENFORCE_PREFERRED_CODEC
VIDEOQUALITY_PREFERRED_CODEC
XMPP_AUTH_DOMAIN={{ jitsi_xmpp_auth_domain }}
XMPP_BOSH_URL_BASE={{ jitsi_xmpp_bosh_url_base }}
XMPP_DOMAIN={{ jitsi_xmpp_domain }}
XMPP_GUEST_DOMAIN={{ jitsi_xmpp_guest_domain }}
XMPP_MUC_DOMAIN={{ jitsi_xmpp_muc_domain }}
XMPP_RECORDER_DOMAIN={{ jitsi_recorder_domain }}

{{ jitsi_web_environment_variables_extension }}
````

## File: templates/web/labels.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

{% if jitsi_web_container_labels_traefik_enabled %}
traefik.enable=true

{% if jitsi_web_container_labels_traefik_docker_network %}
traefik.docker.network={{ jitsi_web_container_labels_traefik_docker_network }}
{% endif %}

{% set middlewares = [] %}

{% if jitsi_web_container_labels_traefik_path_prefix != '/' %}
traefik.http.middlewares.{{ jitsi_identifier }}-web-slashless-redirect.redirectregex.regex=^({{ jitsi_web_container_labels_traefik_path_prefix | quote }})$
traefik.http.middlewares.{{ jitsi_identifier }}-web-slashless-redirect.redirectregex.replacement=${1}/
{% set middlewares = middlewares + [jitsi_identifier + '-web-slashless-redirect'] %}
{% endif %}

{% if jitsi_web_container_labels_traefik_path_prefix != '/' %}
traefik.http.middlewares.{{ jitsi_identifier }}-web-strip-prefix.stripprefix.prefixes={{ jitsi_web_container_labels_traefik_path_prefix }}
{% set middlewares = middlewares + [jitsi_identifier + '-web-strip-prefix'] %}
{% endif %}

{% if jitsi_web_container_labels_traefik_additional_request_headers.keys() | length > 0 %}
{% for name, value in jitsi_web_container_labels_traefik_additional_request_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-web-add-request-headers.headers.customrequestheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-web-add-request-headers'] %}
{% endif %}

{% if jitsi_web_container_labels_traefik_additional_response_headers.keys() | length > 0 %}
{% for name, value in jitsi_web_container_labels_traefik_additional_response_headers.items() %}
traefik.http.middlewares.{{ jitsi_identifier }}-web-add-response-headers.headers.customresponseheaders.{{ name }}={{ value }}
{% endfor %}
{% set middlewares = middlewares + [jitsi_identifier + '-web-add-response-headers'] %}
{% endif %}

traefik.http.routers.{{ jitsi_identifier }}-web.rule={{ jitsi_web_container_labels_traefik_rule }}
{% if jitsi_web_container_labels_traefik_priority | int > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-web.priority={{ jitsi_web_container_labels_traefik_priority }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-web.service={{ jitsi_identifier }}-web
{% if middlewares | length > 0 %}
traefik.http.routers.{{ jitsi_identifier }}-web.middlewares={{ middlewares | join(',') }}
{% endif %}
traefik.http.routers.{{ jitsi_identifier }}-web.entrypoints={{ jitsi_web_container_labels_traefik_entrypoints }}
traefik.http.routers.{{ jitsi_identifier }}-web.tls={{ jitsi_web_container_labels_traefik_tls | to_json }}
{% if jitsi_web_container_labels_traefik_tls %}
traefik.http.routers.{{ jitsi_identifier }}-web.tls.certResolver={{ jitsi_web_container_labels_traefik_tls_certResolver }}
{% endif %}

traefik.http.services.{{ jitsi_identifier }}-web.loadbalancer.server.port=80
{% endif %}

{{ jitsi_web_container_labels_additional_labels }}
````

## File: templates/web/web.service.j2
````
{#
SPDX-FileCopyrightText: 2023 Slavi Pantaleev

SPDX-License-Identifier: AGPL-3.0-or-later
#}

#jinja2: lstrip_blocks: "True"
[Unit]
Description=Jitsi Web server ({{ jitsi_identifier }})
{% for service in jitsi_web_systemd_required_services_list %}
Requires={{ service }}
After={{ service }}
{% endfor %}
DefaultDependencies=no

[Service]
Type=simple
Environment="HOME={{ devture_systemd_docker_base_systemd_unit_home_path }}"
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-web 2>/dev/null || true'
ExecStartPre=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-web 2>/dev/null || true'

ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} create \
			--rm \
			--name={{ jitsi_identifier }}-web \
			--log-driver=none \
			--network={{ jitsi_container_network }} \
			--network-alias={{ jitsi_xmpp_domain }} \
			--env-file={{ jitsi_web_base_path }}/env \
			--label-file={{ jitsi_web_base_path }}/labels \
			{% if jitsi_web_container_http_host_bind_port %}
			-p {{ jitsi_web_container_http_host_bind_port }}:80 \
			{% endif %}
			--mount type=bind,src={{ jitsi_web_config_path }},dst=/config \
			--mount type=bind,src={{ jitsi_web_base_path }}/base.html,dst=/usr/share/jitsi-meet/base.html,ro \
			{% if jitsi_web_well_known_enabled %}
			--mount type=bind,src={{ jitsi_web_well_known_path }},dst=/usr/share/jitsi-meet/.well-known,ro \
			{% endif %}
			--mount type=bind,src={{ jitsi_web_transcripts_path }},dst=/usr/share/jitsi-meet/transcripts \
			--mount type=bind,src={{ jitsi_web_crontabs_path }},dst=/var/spool/cron/crontabs \
			{% for arg in jitsi_web_container_extra_arguments %}
			{{ arg }} \
			{% endfor %}
			{{ jitsi_web_container_image }}

{% for network in jitsi_web_container_additional_networks %}
ExecStartPre={{ devture_systemd_docker_base_host_command_docker }} network connect {{ network }} {{ jitsi_identifier }}-web
{% endfor %}

ExecStart={{ devture_systemd_docker_base_host_command_docker }} start --attach {{ jitsi_identifier }}-web

ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} stop -t {{ devture_systemd_docker_base_container_stop_grace_time_seconds }} {{ jitsi_identifier }}-web 2>/dev/null || true'
ExecStop=-{{ devture_systemd_docker_base_host_command_sh }} -c '{{ devture_systemd_docker_base_host_command_docker }} rm {{ jitsi_identifier }}-web 2>/dev/null || true'
Restart=always
RestartSec=30
SyslogIdentifier={{ jitsi_identifier }}-web

[Install]
WantedBy=multi-user.target
````

## File: justfile
````
# SPDX-FileCopyrightText: 2023 Slavi Pantaleev
#
# SPDX-License-Identifier: AGPL-3.0-or-later

# show help by default
default:
    @just --list --justfile {{ justfile() }}

lint:
    ansible-lint .
````

## File: LICENSE
````
GNU AFFERO GENERAL PUBLIC LICENSE
                       Version 3, 19 November 2007

 Copyright (C) 2007 Free Software Foundation, Inc. <https://fsf.org/>
 Everyone is permitted to copy and distribute verbatim copies
 of this license document, but changing it is not allowed.

                            Preamble

  The GNU Affero General Public License is a free, copyleft license for
software and other kinds of works, specifically designed to ensure
cooperation with the community in the case of network server software.

  The licenses for most software and other practical works are designed
to take away your freedom to share and change the works.  By contrast,
our General Public Licenses are intended to guarantee your freedom to
share and change all versions of a program--to make sure it remains free
software for all its users.

  When we speak of free software, we are referring to freedom, not
price.  Our General Public Licenses are designed to make sure that you
have the freedom to distribute copies of free software (and charge for
them if you wish), that you receive source code or can get it if you
want it, that you can change the software or use pieces of it in new
free programs, and that you know you can do these things.

  Developers that use our General Public Licenses protect your rights
with two steps: (1) assert copyright on the software, and (2) offer
you this License which gives you legal permission to copy, distribute
and/or modify the software.

  A secondary benefit of defending all users' freedom is that
improvements made in alternate versions of the program, if they
receive widespread use, become available for other developers to
incorporate.  Many developers of free software are heartened and
encouraged by the resulting cooperation.  However, in the case of
software used on network servers, this result may fail to come about.
The GNU General Public License permits making a modified version and
letting the public access it on a server without ever releasing its
source code to the public.

  The GNU Affero General Public License is designed specifically to
ensure that, in such cases, the modified source code becomes available
to the community.  It requires the operator of a network server to
provide the source code of the modified version running there to the
users of that server.  Therefore, public use of a modified version, on
a publicly accessible server, gives the public access to the source
code of the modified version.

  An older license, called the Affero General Public License and
published by Affero, was designed to accomplish similar goals.  This is
a different license, not a version of the Affero GPL, but Affero has
released a new version of the Affero GPL which permits relicensing under
this license.

  The precise terms and conditions for copying, distribution and
modification follow.

                       TERMS AND CONDITIONS

  0. Definitions.

  "This License" refers to version 3 of the GNU Affero General Public License.

  "Copyright" also means copyright-like laws that apply to other kinds of
works, such as semiconductor masks.

  "The Program" refers to any copyrightable work licensed under this
License.  Each licensee is addressed as "you".  "Licensees" and
"recipients" may be individuals or organizations.

  To "modify" a work means to copy from or adapt all or part of the work
in a fashion requiring copyright permission, other than the making of an
exact copy.  The resulting work is called a "modified version" of the
earlier work or a work "based on" the earlier work.

  A "covered work" means either the unmodified Program or a work based
on the Program.

  To "propagate" a work means to do anything with it that, without
permission, would make you directly or secondarily liable for
infringement under applicable copyright law, except executing it on a
computer or modifying a private copy.  Propagation includes copying,
distribution (with or without modification), making available to the
public, and in some countries other activities as well.

  To "convey" a work means any kind of propagation that enables other
parties to make or receive copies.  Mere interaction with a user through
a computer network, with no transfer of a copy, is not conveying.

  An interactive user interface displays "Appropriate Legal Notices"
to the extent that it includes a convenient and prominently visible
feature that (1) displays an appropriate copyright notice, and (2)
tells the user that there is no warranty for the work (except to the
extent that warranties are provided), that licensees may convey the
work under this License, and how to view a copy of this License.  If
the interface presents a list of user commands or options, such as a
menu, a prominent item in the list meets this criterion.

  1. Source Code.

  The "source code" for a work means the preferred form of the work
for making modifications to it.  "Object code" means any non-source
form of a work.

  A "Standard Interface" means an interface that either is an official
standard defined by a recognized standards body, or, in the case of
interfaces specified for a particular programming language, one that
is widely used among developers working in that language.

  The "System Libraries" of an executable work include anything, other
than the work as a whole, that (a) is included in the normal form of
packaging a Major Component, but which is not part of that Major
Component, and (b) serves only to enable use of the work with that
Major Component, or to implement a Standard Interface for which an
implementation is available to the public in source code form.  A
"Major Component", in this context, means a major essential component
(kernel, window system, and so on) of the specific operating system
(if any) on which the executable work runs, or a compiler used to
produce the work, or an object code interpreter used to run it.

  The "Corresponding Source" for a work in object code form means all
the source code needed to generate, install, and (for an executable
work) run the object code and to modify the work, including scripts to
control those activities.  However, it does not include the work's
System Libraries, or general-purpose tools or generally available free
programs which are used unmodified in performing those activities but
which are not part of the work.  For example, Corresponding Source
includes interface definition files associated with source files for
the work, and the source code for shared libraries and dynamically
linked subprograms that the work is specifically designed to require,
such as by intimate data communication or control flow between those
subprograms and other parts of the work.

  The Corresponding Source need not include anything that users
can regenerate automatically from other parts of the Corresponding
Source.

  The Corresponding Source for a work in source code form is that
same work.

  2. Basic Permissions.

  All rights granted under this License are granted for the term of
copyright on the Program, and are irrevocable provided the stated
conditions are met.  This License explicitly affirms your unlimited
permission to run the unmodified Program.  The output from running a
covered work is covered by this License only if the output, given its
content, constitutes a covered work.  This License acknowledges your
rights of fair use or other equivalent, as provided by copyright law.

  You may make, run and propagate covered works that you do not
convey, without conditions so long as your license otherwise remains
in force.  You may convey covered works to others for the sole purpose
of having them make modifications exclusively for you, or provide you
with facilities for running those works, provided that you comply with
the terms of this License in conveying all material for which you do
not control copyright.  Those thus making or running the covered works
for you must do so exclusively on your behalf, under your direction
and control, on terms that prohibit them from making any copies of
your copyrighted material outside their relationship with you.

  Conveying under any other circumstances is permitted solely under
the conditions stated below.  Sublicensing is not allowed; section 10
makes it unnecessary.

  3. Protecting Users' Legal Rights From Anti-Circumvention Law.

  No covered work shall be deemed part of an effective technological
measure under any applicable law fulfilling obligations under article
11 of the WIPO copyright treaty adopted on 20 December 1996, or
similar laws prohibiting or restricting circumvention of such
measures.

  When you convey a covered work, you waive any legal power to forbid
circumvention of technological measures to the extent such circumvention
is effected by exercising rights under this License with respect to
the covered work, and you disclaim any intention to limit operation or
modification of the work as a means of enforcing, against the work's
users, your or third parties' legal rights to forbid circumvention of
technological measures.

  4. Conveying Verbatim Copies.

  You may convey verbatim copies of the Program's source code as you
receive it, in any medium, provided that you conspicuously and
appropriately publish on each copy an appropriate copyright notice;
keep intact all notices stating that this License and any
non-permissive terms added in accord with section 7 apply to the code;
keep intact all notices of the absence of any warranty; and give all
recipients a copy of this License along with the Program.

  You may charge any price or no price for each copy that you convey,
and you may offer support or warranty protection for a fee.

  5. Conveying Modified Source Versions.

  You may convey a work based on the Program, or the modifications to
produce it from the Program, in the form of source code under the
terms of section 4, provided that you also meet all of these conditions:

    a) The work must carry prominent notices stating that you modified
    it, and giving a relevant date.

    b) The work must carry prominent notices stating that it is
    released under this License and any conditions added under section
    7.  This requirement modifies the requirement in section 4 to
    "keep intact all notices".

    c) You must license the entire work, as a whole, under this
    License to anyone who comes into possession of a copy.  This
    License will therefore apply, along with any applicable section 7
    additional terms, to the whole of the work, and all its parts,
    regardless of how they are packaged.  This License gives no
    permission to license the work in any other way, but it does not
    invalidate such permission if you have separately received it.

    d) If the work has interactive user interfaces, each must display
    Appropriate Legal Notices; however, if the Program has interactive
    interfaces that do not display Appropriate Legal Notices, your
    work need not make them do so.

  A compilation of a covered work with other separate and independent
works, which are not by their nature extensions of the covered work,
and which are not combined with it such as to form a larger program,
in or on a volume of a storage or distribution medium, is called an
"aggregate" if the compilation and its resulting copyright are not
used to limit the access or legal rights of the compilation's users
beyond what the individual works permit.  Inclusion of a covered work
in an aggregate does not cause this License to apply to the other
parts of the aggregate.

  6. Conveying Non-Source Forms.

  You may convey a covered work in object code form under the terms
of sections 4 and 5, provided that you also convey the
machine-readable Corresponding Source under the terms of this License,
in one of these ways:

    a) Convey the object code in, or embodied in, a physical product
    (including a physical distribution medium), accompanied by the
    Corresponding Source fixed on a durable physical medium
    customarily used for software interchange.

    b) Convey the object code in, or embodied in, a physical product
    (including a physical distribution medium), accompanied by a
    written offer, valid for at least three years and valid for as
    long as you offer spare parts or customer support for that product
    model, to give anyone who possesses the object code either (1) a
    copy of the Corresponding Source for all the software in the
    product that is covered by this License, on a durable physical
    medium customarily used for software interchange, for a price no
    more than your reasonable cost of physically performing this
    conveying of source, or (2) access to copy the
    Corresponding Source from a network server at no charge.

    c) Convey individual copies of the object code with a copy of the
    written offer to provide the Corresponding Source.  This
    alternative is allowed only occasionally and noncommercially, and
    only if you received the object code with such an offer, in accord
    with subsection 6b.

    d) Convey the object code by offering access from a designated
    place (gratis or for a charge), and offer equivalent access to the
    Corresponding Source in the same way through the same place at no
    further charge.  You need not require recipients to copy the
    Corresponding Source along with the object code.  If the place to
    copy the object code is a network server, the Corresponding Source
    may be on a different server (operated by you or a third party)
    that supports equivalent copying facilities, provided you maintain
    clear directions next to the object code saying where to find the
    Corresponding Source.  Regardless of what server hosts the
    Corresponding Source, you remain obligated to ensure that it is
    available for as long as needed to satisfy these requirements.

    e) Convey the object code using peer-to-peer transmission, provided
    you inform other peers where the object code and Corresponding
    Source of the work are being offered to the general public at no
    charge under subsection 6d.

  A separable portion of the object code, whose source code is excluded
from the Corresponding Source as a System Library, need not be
included in conveying the object code work.

  A "User Product" is either (1) a "consumer product", which means any
tangible personal property which is normally used for personal, family,
or household purposes, or (2) anything designed or sold for incorporation
into a dwelling.  In determining whether a product is a consumer product,
doubtful cases shall be resolved in favor of coverage.  For a particular
product received by a particular user, "normally used" refers to a
typical or common use of that class of product, regardless of the status
of the particular user or of the way in which the particular user
actually uses, or expects or is expected to use, the product.  A product
is a consumer product regardless of whether the product has substantial
commercial, industrial or non-consumer uses, unless such uses represent
the only significant mode of use of the product.

  "Installation Information" for a User Product means any methods,
procedures, authorization keys, or other information required to install
and execute modified versions of a covered work in that User Product from
a modified version of its Corresponding Source.  The information must
suffice to ensure that the continued functioning of the modified object
code is in no case prevented or interfered with solely because
modification has been made.

  If you convey an object code work under this section in, or with, or
specifically for use in, a User Product, and the conveying occurs as
part of a transaction in which the right of possession and use of the
User Product is transferred to the recipient in perpetuity or for a
fixed term (regardless of how the transaction is characterized), the
Corresponding Source conveyed under this section must be accompanied
by the Installation Information.  But this requirement does not apply
if neither you nor any third party retains the ability to install
modified object code on the User Product (for example, the work has
been installed in ROM).

  The requirement to provide Installation Information does not include a
requirement to continue to provide support service, warranty, or updates
for a work that has been modified or installed by the recipient, or for
the User Product in which it has been modified or installed.  Access to a
network may be denied when the modification itself materially and
adversely affects the operation of the network or violates the rules and
protocols for communication across the network.

  Corresponding Source conveyed, and Installation Information provided,
in accord with this section must be in a format that is publicly
documented (and with an implementation available to the public in
source code form), and must require no special password or key for
unpacking, reading or copying.

  7. Additional Terms.

  "Additional permissions" are terms that supplement the terms of this
License by making exceptions from one or more of its conditions.
Additional permissions that are applicable to the entire Program shall
be treated as though they were included in this License, to the extent
that they are valid under applicable law.  If additional permissions
apply only to part of the Program, that part may be used separately
under those permissions, but the entire Program remains governed by
this License without regard to the additional permissions.

  When you convey a copy of a covered work, you may at your option
remove any additional permissions from that copy, or from any part of
it.  (Additional permissions may be written to require their own
removal in certain cases when you modify the work.)  You may place
additional permissions on material, added by you to a covered work,
for which you have or can give appropriate copyright permission.

  Notwithstanding any other provision of this License, for material you
add to a covered work, you may (if authorized by the copyright holders of
that material) supplement the terms of this License with terms:

    a) Disclaiming warranty or limiting liability differently from the
    terms of sections 15 and 16 of this License; or

    b) Requiring preservation of specified reasonable legal notices or
    author attributions in that material or in the Appropriate Legal
    Notices displayed by works containing it; or

    c) Prohibiting misrepresentation of the origin of that material, or
    requiring that modified versions of such material be marked in
    reasonable ways as different from the original version; or

    d) Limiting the use for publicity purposes of names of licensors or
    authors of the material; or

    e) Declining to grant rights under trademark law for use of some
    trade names, trademarks, or service marks; or

    f) Requiring indemnification of licensors and authors of that
    material by anyone who conveys the material (or modified versions of
    it) with contractual assumptions of liability to the recipient, for
    any liability that these contractual assumptions directly impose on
    those licensors and authors.

  All other non-permissive additional terms are considered "further
restrictions" within the meaning of section 10.  If the Program as you
received it, or any part of it, contains a notice stating that it is
governed by this License along with a term that is a further
restriction, you may remove that term.  If a license document contains
a further restriction but permits relicensing or conveying under this
License, you may add to a covered work material governed by the terms
of that license document, provided that the further restriction does
not survive such relicensing or conveying.

  If you add terms to a covered work in accord with this section, you
must place, in the relevant source files, a statement of the
additional terms that apply to those files, or a notice indicating
where to find the applicable terms.

  Additional terms, permissive or non-permissive, may be stated in the
form of a separately written license, or stated as exceptions;
the above requirements apply either way.

  8. Termination.

  You may not propagate or modify a covered work except as expressly
provided under this License.  Any attempt otherwise to propagate or
modify it is void, and will automatically terminate your rights under
this License (including any patent licenses granted under the third
paragraph of section 11).

  However, if you cease all violation of this License, then your
license from a particular copyright holder is reinstated (a)
provisionally, unless and until the copyright holder explicitly and
finally terminates your license, and (b) permanently, if the copyright
holder fails to notify you of the violation by some reasonable means
prior to 60 days after the cessation.

  Moreover, your license from a particular copyright holder is
reinstated permanently if the copyright holder notifies you of the
violation by some reasonable means, this is the first time you have
received notice of violation of this License (for any work) from that
copyright holder, and you cure the violation prior to 30 days after
your receipt of the notice.

  Termination of your rights under this section does not terminate the
licenses of parties who have received copies or rights from you under
this License.  If your rights have been terminated and not permanently
reinstated, you do not qualify to receive new licenses for the same
material under section 10.

  9. Acceptance Not Required for Having Copies.

  You are not required to accept this License in order to receive or
run a copy of the Program.  Ancillary propagation of a covered work
occurring solely as a consequence of using peer-to-peer transmission
to receive a copy likewise does not require acceptance.  However,
nothing other than this License grants you permission to propagate or
modify any covered work.  These actions infringe copyright if you do
not accept this License.  Therefore, by modifying or propagating a
covered work, you indicate your acceptance of this License to do so.

  10. Automatic Licensing of Downstream Recipients.

  Each time you convey a covered work, the recipient automatically
receives a license from the original licensors, to run, modify and
propagate that work, subject to this License.  You are not responsible
for enforcing compliance by third parties with this License.

  An "entity transaction" is a transaction transferring control of an
organization, or substantially all assets of one, or subdividing an
organization, or merging organizations.  If propagation of a covered
work results from an entity transaction, each party to that
transaction who receives a copy of the work also receives whatever
licenses to the work the party's predecessor in interest had or could
give under the previous paragraph, plus a right to possession of the
Corresponding Source of the work from the predecessor in interest, if
the predecessor has it or can get it with reasonable efforts.

  You may not impose any further restrictions on the exercise of the
rights granted or affirmed under this License.  For example, you may
not impose a license fee, royalty, or other charge for exercise of
rights granted under this License, and you may not initiate litigation
(including a cross-claim or counterclaim in a lawsuit) alleging that
any patent claim is infringed by making, using, selling, offering for
sale, or importing the Program or any portion of it.

  11. Patents.

  A "contributor" is a copyright holder who authorizes use under this
License of the Program or a work on which the Program is based.  The
work thus licensed is called the contributor's "contributor version".

  A contributor's "essential patent claims" are all patent claims
owned or controlled by the contributor, whether already acquired or
hereafter acquired, that would be infringed by some manner, permitted
by this License, of making, using, or selling its contributor version,
but do not include claims that would be infringed only as a
consequence of further modification of the contributor version.  For
purposes of this definition, "control" includes the right to grant
patent sublicenses in a manner consistent with the requirements of
this License.

  Each contributor grants you a non-exclusive, worldwide, royalty-free
patent license under the contributor's essential patent claims, to
make, use, sell, offer for sale, import and otherwise run, modify and
propagate the contents of its contributor version.

  In the following three paragraphs, a "patent license" is any express
agreement or commitment, however denominated, not to enforce a patent
(such as an express permission to practice a patent or covenant not to
sue for patent infringement).  To "grant" such a patent license to a
party means to make such an agreement or commitment not to enforce a
patent against the party.

  If you convey a covered work, knowingly relying on a patent license,
and the Corresponding Source of the work is not available for anyone
to copy, free of charge and under the terms of this License, through a
publicly available network server or other readily accessible means,
then you must either (1) cause the Corresponding Source to be so
available, or (2) arrange to deprive yourself of the benefit of the
patent license for this particular work, or (3) arrange, in a manner
consistent with the requirements of this License, to extend the patent
license to downstream recipients.  "Knowingly relying" means you have
actual knowledge that, but for the patent license, your conveying the
covered work in a country, or your recipient's use of the covered work
in a country, would infringe one or more identifiable patents in that
country that you have reason to believe are valid.

  If, pursuant to or in connection with a single transaction or
arrangement, you convey, or propagate by procuring conveyance of, a
covered work, and grant a patent license to some of the parties
receiving the covered work authorizing them to use, propagate, modify
or convey a specific copy of the covered work, then the patent license
you grant is automatically extended to all recipients of the covered
work and works based on it.

  A patent license is "discriminatory" if it does not include within
the scope of its coverage, prohibits the exercise of, or is
conditioned on the non-exercise of one or more of the rights that are
specifically granted under this License.  You may not convey a covered
work if you are a party to an arrangement with a third party that is
in the business of distributing software, under which you make payment
to the third party based on the extent of your activity of conveying
the work, and under which the third party grants, to any of the
parties who would receive the covered work from you, a discriminatory
patent license (a) in connection with copies of the covered work
conveyed by you (or copies made from those copies), or (b) primarily
for and in connection with specific products or compilations that
contain the covered work, unless you entered into that arrangement,
or that patent license was granted, prior to 28 March 2007.

  Nothing in this License shall be construed as excluding or limiting
any implied license or other defenses to infringement that may
otherwise be available to you under applicable patent law.

  12. No Surrender of Others' Freedom.

  If conditions are imposed on you (whether by court order, agreement or
otherwise) that contradict the conditions of this License, they do not
excuse you from the conditions of this License.  If you cannot convey a
covered work so as to satisfy simultaneously your obligations under this
License and any other pertinent obligations, then as a consequence you may
not convey it at all.  For example, if you agree to terms that obligate you
to collect a royalty for further conveying from those to whom you convey
the Program, the only way you could satisfy both those terms and this
License would be to refrain entirely from conveying the Program.

  13. Remote Network Interaction; Use with the GNU General Public License.

  Notwithstanding any other provision of this License, if you modify the
Program, your modified version must prominently offer all users
interacting with it remotely through a computer network (if your version
supports such interaction) an opportunity to receive the Corresponding
Source of your version by providing access to the Corresponding Source
from a network server at no charge, through some standard or customary
means of facilitating copying of software.  This Corresponding Source
shall include the Corresponding Source for any work covered by version 3
of the GNU General Public License that is incorporated pursuant to the
following paragraph.

  Notwithstanding any other provision of this License, you have
permission to link or combine any covered work with a work licensed
under version 3 of the GNU General Public License into a single
combined work, and to convey the resulting work.  The terms of this
License will continue to apply to the part which is the covered work,
but the work with which it is combined will remain governed by version
3 of the GNU General Public License.

  14. Revised Versions of this License.

  The Free Software Foundation may publish revised and/or new versions of
the GNU Affero General Public License from time to time.  Such new versions
will be similar in spirit to the present version, but may differ in detail to
address new problems or concerns.

  Each version is given a distinguishing version number.  If the
Program specifies that a certain numbered version of the GNU Affero General
Public License "or any later version" applies to it, you have the
option of following the terms and conditions either of that numbered
version or of any later version published by the Free Software
Foundation.  If the Program does not specify a version number of the
GNU Affero General Public License, you may choose any version ever published
by the Free Software Foundation.

  If the Program specifies that a proxy can decide which future
versions of the GNU Affero General Public License can be used, that proxy's
public statement of acceptance of a version permanently authorizes you
to choose that version for the Program.

  Later license versions may give you additional or different
permissions.  However, no additional obligations are imposed on any
author or copyright holder as a result of your choosing to follow a
later version.

  15. Disclaimer of Warranty.

  THERE IS NO WARRANTY FOR THE PROGRAM, TO THE EXTENT PERMITTED BY
APPLICABLE LAW.  EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT
HOLDERS AND/OR OTHER PARTIES PROVIDE THE PROGRAM "AS IS" WITHOUT WARRANTY
OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO,
THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
PURPOSE.  THE ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE PROGRAM
IS WITH YOU.  SHOULD THE PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF
ALL NECESSARY SERVICING, REPAIR OR CORRECTION.

  16. Limitation of Liability.

  IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR CONVEYS
THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES, INCLUDING ANY
GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE
USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT NOT LIMITED TO LOSS OF
DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR THIRD
PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER PROGRAMS),
EVEN IF SUCH HOLDER OR OTHER PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF
SUCH DAMAGES.

  17. Interpretation of Sections 15 and 16.

  If the disclaimer of warranty and limitation of liability provided
above cannot be given local legal effect according to their terms,
reviewing courts shall apply local law that most closely approximates
an absolute waiver of all civil liability in connection with the
Program, unless a warranty or assumption of liability accompanies a
copy of the Program in return for a fee.

                     END OF TERMS AND CONDITIONS

            How to Apply These Terms to Your New Programs

  If you develop a new program, and you want it to be of the greatest
possible use to the public, the best way to achieve this is to make it
free software which everyone can redistribute and change under these terms.

  To do so, attach the following notices to the program.  It is safest
to attach them to the start of each source file to most effectively
state the exclusion of warranty; and each file should have at least
the "copyright" line and a pointer to where the full notice is found.

    <one line to give the program's name and a brief idea of what it does.>
    Copyright (C) <year>  <name of author>

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU Affero General Public License as published
    by the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU Affero General Public License for more details.

    You should have received a copy of the GNU Affero General Public License
    along with this program.  If not, see <https://www.gnu.org/licenses/>.

Also add information on how to contact you by electronic and paper mail.

  If your software can interact with users remotely through a computer
network, you should also make sure that it provides a way for users to
get its source.  For example, if your program is a web application, its
interface could display a "Source" link that leads users to an archive
of the code.  There are many ways you could offer source, and different
solutions will be better for different programs; see section 13 for the
specific requirements.

  You should also get your employer (if you work as a programmer) or school,
if any, to sign a "copyright disclaimer" for the program, if necessary.
For more information on this, and how to apply and follow the GNU AGPL, see
<https://www.gnu.org/licenses/>.
````

## File: README.md
````markdown
<!--
SPDX-FileCopyrightText: 2023 Slavi Pantaleev
SPDX-FileCopyrightText: 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Jitsi Ansible role

[![REUSE status](https://api.reuse.software/badge/github.com/mother-of-all-self-hosting/ansible-role-jitsi)](https://api.reuse.software/info/github.com/mother-of-all-self-hosting/ansible-role-jitsi)

This is an [Ansible](https://www.ansible.com/) role which installs [Jitsi](https://jitsi.org/) to run as a bunch of [Docker](https://www.docker.com/) containers wrapped in systemd services. The architecture is inspired by the [docker-jitsi-meet](https://github.com/jitsi/docker-jitsi-meet) project, but does not use Docker Compose.

This role *implicitly* depends on:

- [`com.devture.ansible.role.playbook_help`](https://github.com/devture/com.devture.ansible.role.playbook_help)
- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

For an Ansible playbook which integrates this role and makes it easier to use, see the [mash-playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

💡 See this [document](docs/configuring-jitsi.md) for details about setting up the service with this role.
````

## File: REUSE.toml
````toml
# SPDX-FileCopyrightText: 2025 Suguru Hirahara
#
# SPDX-License-Identifier: AGPL-3.0-or-later

version = 1

# Computer-generated files and other files which cannot be copyrighted
[[annotations]]
path = [
    "meta/main.yml",
]
SPDX-FileCopyrightText = "NONE"
SPDX-License-Identifier = "CC0-1.0"
````
