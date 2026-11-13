# ns8-frappe-press

A powerful open source platform for building business applications: Frappe Press supports ERP, blogging, CRM, and more

Module id: `FRAPPEPRESS`. Images are pinned in `build-images.sh` and updated through reviewed changes.

## Install

```bash
add-module ghcr.io/geniusdynamics/frappe-press:latest 1
```

The command returns the instance id, for example `FRAPPEPRESS1`. Use the id NS8 returns.

## Configure

```bash
api-cli run configure-module --agent module/FRAPPEPRESS1 --data - <<'EOF'
{
  "host": "frappe-press.example.org",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```

| Parameter | Required | Type | Description |
|---|---|---|---|
| `host` | yes | string | Host name for the application, like 'erpnext.domain.org' |
| `lets_encrypt` | yes | boolean | Request a valid Let's Encrypt certificate. |
| `http2https` | yes | boolean | Redirect all the HTTP requests to HTTPS |

## Configuration output

`get-configuration` returns: `host`, `lets_encrypt`, `http2https`.

## Images

| Image | Version |
|---|---|
| `docker.io/mariadb` | `13.0.2` |
| `docker.io/geniusdynamics/press` | `0.3.0` |
| `docker.io/redis` | `8.10.3-alpine3.23` |

## Network and access

- Reserves 1 TCP port(s) on the node. The web application is published through Traefik on the configured host name.
- Runs rootless.
- Declares these NS8 authorizations: `traefik@node:routeadm`.

## Backup

The following paths are included in the NS8 backup (`imageroot/etc/state-include.conf`):

- `state/backup_paths.env`
- `state/database.env`
- `state/configurator.env`
- `state/erpnext-modules.env`
- `volumes/sites`
- `volumes/logs`

## Services

- `backend.service`
- `configurator.service`
- `create-site.service`
- `erp-next.service`
- `frontend.service`
- `mariadb-app.service`
- `queue-long.service`
- `queue-short.service`
- `redis-cache.service`
- `redis-queue.service`
- `redis-socketio.service`
- `scheduler.service`
- `websocket.service`

## Get the configuration

```bash
api-cli run get-configuration --agent module/FRAPPEPRESS1
```

## Update and uninstall

```bash
api-cli run update-module --data '{"module_url":"ghcr.io/geniusdynamics/frappe-press:latest","instances":["FRAPPEPRESS1"],"force":true}'
remove-module --no-preserve FRAPPEPRESS1
```

## Testing

Continuous integration:

- `Publish images` builds the module image on every push.
- `Test module` installs the image on a QEMU NS8 node and runs the Robot Framework suite in `tests/`. It runs after `Publish images` succeeds on `dev` and on `main`.
- `Build API JSON Schema docs` and the security scan run alongside.

The suite installs the module, configures a host name, checks that the web application answers through Traefik, takes UI screenshots and removes the module.

Run it locally with the NS8 standard infrastructure described in the [ns8-github-actions README](https://github.com/NethServer/ns8-github-actions/blob/v1/README.md#running-tests-locally).

## Open items

Found by the NS8 module standards check. These must be resolved before a stable release:

- tests/frappe-press.robot generated from template: set real configure payload and URL check
- mariadb-app.service: uses %S/state, kickstart uses %E/state
- frontend.service: uses %S/state, kickstart uses %E/state
- queue-short.service: uses %S/state, kickstart uses %E/state
- backend.service: uses %S/state, kickstart uses %E/state
- create-site.service: uses %S/state, kickstart uses %E/state
- scheduler.service: uses %S/state, kickstart uses %E/state
- configurator.service: uses %S/state, kickstart uses %E/state
- websocket.service: uses %S/state, kickstart uses %E/state
- redis-cache.service: uses %S/state, kickstart uses %E/state
- redis-socketio.service: uses %S/state, kickstart uses %E/state
- erp-next.service: uses %S/state, kickstart uses %E/state
- redis-queue.service: uses %S/state, kickstart uses %E/state
- queue-long.service: uses %S/state, kickstart uses %E/state
- discover-smarthost still present: remove if the app does not use SMTP
- mariadb-app.service: %S/state changed to %E/state (review the bind mounts that remain)
- frontend.service: %S/state changed to %E/state (review the bind mounts that remain)
- queue-short.service: %S/state changed to %E/state (review the bind mounts that remain)
- backend.service: %S/state changed to %E/state (review the bind mounts that remain)
- create-site.service: %S/state changed to %E/state (review the bind mounts that remain)
- scheduler.service: %S/state changed to %E/state (review the bind mounts that remain)
- configurator.service: %S/state changed to %E/state (review the bind mounts that remain)
- websocket.service: %S/state changed to %E/state (review the bind mounts that remain)
- redis-cache.service: %S/state changed to %E/state (review the bind mounts that remain)
- redis-socketio.service: %S/state changed to %E/state (review the bind mounts that remain)
- erp-next.service: %S/state changed to %E/state (review the bind mounts that remain)
- redis-queue.service: %S/state changed to %E/state (review the bind mounts that remain)
- queue-long.service: %S/state changed to %E/state (review the bind mounts that remain)
- imageroot/etc/state-include.conf: smarthost lines removed, check the unit still starts
- imageroot/actions/restore-module/60start_services: smarthost lines removed, check the unit still starts
- imageroot/actions/configure-module/80start_services: smarthost lines removed, check the unit still starts
- PRESS_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- REDIS_IMAGE is used by the module but no image in the label provides it: leftover from the template?

## Upstream

Application documentation: https://frappeframework.com/docs/

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
