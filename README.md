# FIDO Polkit Agent

An Omarchy Polkit authentication agent override that clearly shows when PAM is
waiting for a FIDO security key.

When `pam_u2f.so` is configured in `/etc/pam.d/polkit-1`, the dialog shows:

```text
Touch your security key
```

The password field appears only after PAM requests a password. Fingerprint
authentication remains supported.

## Local Setup

This repository is intended to be developed from `~/Projects` and linked as a
whole plugin directory. Do not symlink individual files.

```bash
mkdir -p ~/.config/omarchy/plugins
ln -sfn ~/Projects/omarchy-polkit ~/.config/omarchy/plugins/peti.polkit
OMARCHY_PATH=/usr/share/omarchy omarchy plugin validate ~/Projects/omarchy-polkit
OMARCHY_PATH=/usr/share/omarchy omarchy plugin enable peti.polkit
omarchy-shell shell rescanPlugins
OMARCHY_PATH=/usr/share/omarchy omarchy restart shell
```

The validation command must point at the real project directory because the
Omarchy validator rejects a symlink passed as the plugin-folder argument.

## Test

Run an action that uses Polkit:

```bash
pkexec /usr/bin/id
```

With an enrolled FIDO key, the dialog should ask you to touch it first. If FIDO
authentication fails, the password field should appear as the PAM fallback.

## Files

- `manifest.json` declares the Omarchy service plugin.
- `PolkitAgent.qml` provides the dialog and authentication flow.
- `PolkitModel.js` detects FIDO and fingerprint PAM modules.
