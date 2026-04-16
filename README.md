Davinci Resolve expects IOPlugin extensions to be named *.dvcp.bundle. However, Flatpak doesn't directly support periods in the extension ID (which is in turn used to derive FLATPAK_DEST). So we have to do some contortions to build and bundle these plugins for Davinci Resolve:
```
flatpak install flathub org.freedesktop.Sdk//24.08
flatpak-builder --disable-rofiles-fuse --force-clean .build-dir com.blackmagic.ResolveStudio.ioplugin.ffmpeg_encoder_plugin.dvcp.bundle.yaml
sed -Ei 's/name=(.+)_dvcp_bundle/name=\1.dvcp.bundle/g' .build-dir/metadata
flatpak build-export --arch=x86_64 --update-appstream '--exclude=/lib/debug/*' --include=/lib/debug/app '--exclude=/share/runtime/locale/*/*' .repo .build-dir 1.0
```
And install with:

```
flatpak --user remote-add --no-gpg-verify ffmpeg-resolve .repo
flatpak --user install ffmpeg-resolve com.blackmagic.ResolveStudio.ioplugin.ffmpeg_encoder_plugin.dvcp.bundle
```
