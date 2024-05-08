# HickelSOFT RustDesk Custom Client

Based on the current "Nightly build".

Changes
- Standalone Server - Only for use for HickelSOFT Huth GmbH customers! (Secrets: `RENDEZVOUS_SERVER`, `API_SERVER`, and `RS_PUB_KEY`)
- 5 default settings applied: Zoom Cursor, Show Remote Cursor, Show Monitors Toolbar, Adaptive View Style, Enable File Transfer ([Source file: config.rs](https://github.com/hickelsoft/rustdesk/blob/master/libs/hbb_common/src/config.rs#L1558))
- Builds only for Win32 and Win64 ([Source file: flutter-build.yml](https://github.com/hickelsoft/rustdesk/blob/master/.github/workflows/flutter-build.yml))
- Change some German texts for HickelSOFT ([Source file: de.rs](https://github.com/hickelsoft/rustdesk/blob/master/src/lang/de.rs))
- Removed "sad smiley" for empty peers list, since it confuses customers ([Source file: peers_view.dart](https://github.com/hickelsoft/rustdesk/blob/master/flutter/lib/common/widgets/peers_view.dart), [Commit](https://github.com/hickelsoft/rustdesk/commit/09a23f1cef26cf4cc72430bced720af3e7321aaf))
- Added "rebase" command to GitHub Desktop UI ([Source file: .gitconfig](https://github.com/hickelsoft/rustdesk/blob/master/.gitconfig))
- Disabled `secure_tcp`, because our server does not support `KeyExchange`. ([Source file: common.rs](https://github.com/hickelsoft/rustdesk/blob/master/src/common.rs))
