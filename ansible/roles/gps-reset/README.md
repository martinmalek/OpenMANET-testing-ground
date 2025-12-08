# gps-reset

Removes GPS initialization configuration and puts the GPS module into standby mode. This role is useful for disabling GPS functionality or resetting GPS configuration to factory settings.

## Tasks

- Disables GPS init script at boot (removes `/etc/rc.d/S15gps-init`)
- Puts GPS in standby mode using the init script (if it exists)
- Puts GPS in standby mode via GPIO (sets WAKE pin to HIGH)
- Removes the GPS init script (`/etc/init.d/gps-init`)

## Variables

Variables are defined in `defaults/main.yml`:

- `gps_gpio_rst`: GPIO pin for GPS reset (default: `25`)
- `gps_gpio_wake`: GPIO pin for GPS wake (default: `12`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
# Reset GPS configuration on specific device
ansible-playbook playbooks/site.yml --tags gps-reset --limit gateway
```

## Notes

This role will completely remove GPS initialization and put the GPS module in standby mode. To re-enable GPS, run the `gps` role again.
