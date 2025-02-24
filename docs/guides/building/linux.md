---
sidebar_position: 4
---

import TauriBuild from './\_tauri-build.md'

# Linux Bundle

Tauri applications for Linux are distributed either with a Debian bundle (`.deb` file) or an AppImage (`.AppImage` file). The Tauri CLI automatically bundles your application code in these formats by default. Please note that `.deb` and `.AppImage` bundles can **only be created on Linux** as cross-compilation doesn't work yet.

:::note

GUI apps on macOS and Linux do not inherit the `$PATH` from your shell dotfiles (`.bashrc`, `.bash_profile`, `.zshrc`, etc). Check out Tauri's [fix-path-env-rs] crate to fix this issue.

:::

<TauriBuild />

## Limitations

Core libraries such as glibc frequently break compatibility with older systems. For this reason, you must build your Tauri application using the oldest base system you intend to support. A relatively old system such as Ubuntu 18.04 is more suited than Ubuntu 22.04, as the binary compiled on Ubuntu 22.04 will have a higher requirement of the glibc version, so when running on an older system, you will face a runtime error like `/usr/lib/libc.so.6: version 'GLIBC_2.33' not found`. We recommend using a Docker container or GitHub Actions to build your Tauri application for Linux.

See the issues [tauri-apps/tauri#1355] and [rust-lang/rust#57497], in addition to the [AppImage guide] for more information.

## Debian

The stock Debian package generated by the Tauri bundler has everything you need to ship your application to Debian-based Linux distributions, defining your application's icons, generating a Desktop file, and specifying the dependencies `libwebkit2gtk-4.0-37` and `libgtk-3-0`, along with `libappindicator3-1` if your app uses the system tray.

### Custom Files

Tauri exposes a few configurations for the Debian package in case you need more control.

If your app depends on additional system dependencies you can specify them in `tauri.conf.json > tauri > bundle > deb > depends`.

To include custom files in the Debian package, you can provide a list of files or folders in `tauri.conf.json > tauri > bundle > deb > files`. The configuration object maps the path in the Debian package to the path to the file on your filesystem, relative to the `tauri.conf.json` file. Here's an example configuration:

```json
{
  "tauri": {
    "bundle": {
      "deb": {
        "files": {
          "/usr/share/README.md": "../README.md", // copies the README.md file to /usr/share/README.md
          "usr/share/assets": "../assets/" // copies the entire assets directory to /usr/share/assets
        }
      }
    }
  }
}
```

If you need to bundle files in a cross-platform way, check Tauri's [resource] and [sidecar] mechanisms.

## AppImage

AppImage is a distribution format that does not rely on the system installed packages and instead bundles all dependencies and files needed by the application. For this reason, the output file is larger but easier to distribute since it is supported on many Linux distributions and can be executed without installation. The user just needs to make the file executable (`chmod a+x MyProject.AppImage`) and can then run it (`./MyProject.AppImage`).

AppImages are convenient, simplifying the distribution process if you cannot make a package targeting the distribution's package manager. Still, you should carefully use it as the file size grows from the 2-6MBs range to 70+MBs.

:::caution

If your app plays audio/video you need to enable `tauri.conf.json > tauri > bundle > appimage > bundleMediaFramework`. This will increase the size of the AppImage bundle to include addition `gstreamer` files needed for media playback. This flag is currently only supported on Ubuntu build systems.

:::

[resource]: resources.md
[sidecar]: sidecar.md
[debian package]: https://wiki.debian.org/Packaging
[appimage]: https://appimage.org/
[tauri-apps/tauri#1355]: https://github.com/tauri-apps/tauri/issues/1355
[rust-lang/rust#57497]: https://github.com/rust-lang/rust/issues/57497
[appimage guide]: https://docs.appimage.org/reference/best-practices.html#binaries-compiled-on-old-enough-base-system
