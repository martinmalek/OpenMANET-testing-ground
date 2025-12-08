# opentakserver-packages

Uploads ATAK plugin APK files from GitHub releases to OpenTAKServer. Uses token authentication as documented in the [OpenTAKServer authentication guide](https://docs.opentakserver.io/authentication.html). Requires administrator role.

## Tasks

- Downloads APK files from GitHub release
- Authenticates with OpenTAKServer using token authentication
- Uploads APK files to OpenTAKServer via `/api/packages` API endpoint
- Cleans up temporary download files

## Variables

Variables are defined in `defaults/main.yml`:

- `opentakserver_host`: OpenTAKServer hostname or IP (default: `{{ ansible_host | default(inventory_hostname) }}`)
- `opentakserver_port`: OpenTAKServer port (default: `8880`)
- `opentakserver_username`: Username for authentication (default: `administrator`)
- `opentakserver_password`: Password for authentication (default: `password`)
- `packages_release_tag`: GitHub release tag (default: `opentakserver-atak-apks`)
- `packages_release_url`: GitHub release URL (default: constructed from release tag)
- `packages_files`: List of package files to upload with metadata (default: includes 3 APK files, .xapk files commented out)
- `packages_temp_dir`: Temporary directory for downloads (default: `/tmp/packages_uploads`)

Each package file entry can include:
- `name`: Filename
- `url`: Download URL
- `platform`: Platform (default: `Android`)
- `plugin_type`: Plugin type (default: `plugin`)
- `atak_version`: ATAK version (optional)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
# Upload ATAK packages to OpenTAKServer on gateway
ansible-playbook playbooks/site.yml --tags ots-packages --limit gateway
```

## Notes

- The role targets the gateway device (where OpenTAKServer runs) but all tasks are delegated to `localhost` (the control machine) since they need to download files from GitHub and make API calls. The `opentakserver_host` variable automatically uses the target device's `ansible_host` to connect to the correct OpenTAKServer instance.
- This role requires administrator privileges. Only `.apk` files are supported by the API. `.xapk` files (which are ZIP archives) need to be extracted first before uploading.

## Reference

[OpenTAKServer Package API](https://github.com/brian7704/OpenTAKServer/blob/master/opentakserver/blueprints/ots_api/package_api.py)

## ATAK Plugins

The following ATAK plugins are available for download from APKPure. These can be uploaded to OpenTAKServer once you have a hosting location:

- **UAS Tool**: https://apkpure.com/atak-plugin-uas-tool/com.atakmap.android.uastool.plugin
- **Data Sync**: https://apkpure.com/datasync/com.atakmap.android.datasync.plugin
- **Drone Hone**: https://apkpure.com/dronehone/com.atakmap.android.dronehone.plugin
- **TAK Chat**: https://apkpure.com/takchat/com.atakmap.android.takchat.plugin
- **GBR VX**: https://apkpure.com/gbr.vx/com.atakmap.android.gbr.vx.plugin
- **GeoCam**: https://apkpure.com/geocam/com.atakmap.android.geocam.plugin
- **Compass Nav**: https://apkpure.com/atak-plugin-compass-nav/com.atakmap.android.compassnav.plugin
- **WASP**: https://apkpure.com/atak-plugin-wasp/com.atakmap.android.wasp.plugin
- **VNS**: https://apkpure.com/atak-plugin-vns/com.atakmap.android.vns.plugin
- **GRG Builder**: https://apkpure.com/atak-plugin-grg-builder/com.atakmap.android.grgbuilder.plugin
- **VideoWall**: https://apkpure.com/atak-plugin-videowall/com.atakmap.android.gbr.multivideo.plugin
- **PDF** https://apkpure.com/atak-plugin-pdf/com.atakmap.android.gbr.pdf.plugin
- **Meshtastic**: https://github.com/meshtastic/ATAK-Plugin/releases/download/1.1.23/ATAK-Plugin-Meshtastic.Plugin-1.1.23-bdcc9b10-5.6.0-civ-release.apk
