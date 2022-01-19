# Flatpak-Overrides
The default permissions for Flatpak applications from FlatHub are generally very permissive and allow broad filesystem access with `filesystem=home` or `filesystem=host` and devices access with `device=all`. This repository contains a set of overrides to to help **reduce** this problem with the default laxed permissions.

These high level user facing configurations will not fix the low level issues with Flatpak, including /proc and /sys access, a very limited syscall blacklist, and so on. It cannot solve the issue of developers not updating their software to work with portals which leads to direct filesystem access still needing to be granted.

# Wayland
All of these configurations assume that you are on a Wayland system. Flatpak does not provide any sandboxing option for X11, and X11 lacks GUI isolation, making any attempt of sandboxing futile. In most cases, if an application works with Wayland natively (not requiring XWayland to run), access to the x11 socket and the fallback-x11 socket will be explicitly revoked to force the application to run in a Wayland window at all times.

# filesystem=home, filesystem=host, and filesystem=host-etc access
Access to the entire home directory, the host system, or /etc are inherently dangerous, as a malicious application can just add whatever malicious commands into the shell profile. For this reason, this set of overrides will revoke all of those permissions and replace them with commonly used directories if possible.

# socket=pulseaudio
PulseAudio socket access is extremely problematic as it allows the application to both play and record audio. There is no way to block an application from accessing the microphone if this socket is being used. For applications which normally do not need to play audio (like OnlyOffice), access to this socket will be revoked.

# device=all
`device=all` access is also very problematic, as it provides access to all devices, including the camera. At the same time, device=all may be needed to use security keys for authentication. This set of configurations does not revoke `device=all` for applications which do need it to work with security keys. However, if you do not use a security at all, you should consider revoking it in your own configuration.

# Brave
Brave, like Chromium browsers and electron apps, can work with Wayland via the `--enable-features=UseOzonePlatform --ozone-platform=wayland` flag, though your mileage for each application may vary. I have personally tested Brave with this flag and it works just fine, thus access to the X11 socket is revoked as described above. Brave browser will not launch until you have copied the .desktop file from `~/.local/share/flatpak/exports/share/applications` to `/.local/share/applications` and `--enable-features=UseOzonePlatform --ozone-platform=wayland` to the `Exec` lines.
