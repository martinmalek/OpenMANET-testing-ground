# gps

Configures GPS initialization for WM1302 Pi Hat with Quectel L76K GNSS module.

## Tasks

- Deploys GPS initialization script
- Configures GPS to run on boot via `/etc/rc.local`
- Sets up GPIO pins for GPS reset and wake control

## Variables

Variables are defined in `defaults/main.yml`:

- `gps_tty_device`: GPS serial port (default: `/dev/ttyAMA0`)
- `gps_gpio_rst`: GPIO pin for GPS reset (default: `25`)
- `gps_gpio_wake`: GPIO pin for GPS wake (default: `12`)
- `gps_baud`: GPS baud rate (default: `9600`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
ansible-playbook playbooks/site.yml --tags gps
```

## Notes

GPS setup requires WM1302 Pi Hat hardware. The GPS will start automatically on boot.

## Testing GPS

You can test GPS connectivity using `gpspipe` or `gpsmon` to connect to the gpsd daemon:

```bash
# View raw NMEA data
gpspipe

# View formatted GPS data
gpsmon 127.0.0.1:2947
```

The gpsd daemon listens on `127.0.0.1:2947` by default.
