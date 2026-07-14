# ykding

Plays a sound and shows a notification whenever your YubiKey is waiting for
a touch on macOS. One ding = one touch owed.

Useful when the key is plugged in out of sight (iMac rear ports, docks). A
rebase that re-signs five commits dings after each touch until all five are
done.

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/onyb/ykding/master/ykding | zsh -s install
```

This installs the latest release to `~/.local/bin/ykding` and starts a
LaunchAgent (`com.ykding`) that runs at login, playing the alert once so
you know the sound. Update later with `ykding update`; it never
downgrades. Both commands take a `nightly` argument to get the tip of
master instead. If no banner
appears, allow notifications for **Script Editor** in System Settings →
Notifications.

ykding has zero dependencies. It only uses tools that ship with macOS
(`log`, `afplay`, `osascript`, `launchctl`).

## What you get

- **Glass** ding + banner for FIDO2 touches (`sk-ssh-*` keys, git commit
  signing, SSH auth).
- **Ping** ding + banner for OpenPGP touches (gpg on the YubiKey).
- When git signing triggered the touch, the banner shows the repo and
  the commit subject:

  > **Touch your YubiKey (FIDO2)**
  > onyb/ykding
  > feat: alert with sound and banner when a YubiKey waits for touch

## Commands

| Command            | What it does                                     |
| ------------------ | ------------------------------------------------ |
| `ykding install`   | Install the latest release as a LaunchAgent      |
| `ykding update`    | Update to the newest release                     |
| `ykding uninstall` | Stop and remove it                               |
| `ykding test`      | Play both alerts once                            |
| `ykding run`       | Run in the foreground                            |
| `ykding version`   | Print the version                                |

Logs go to `~/Library/Logs/ykding.log`, one line per touch request.

## How it works

While a YubiKey waits for a touch, macOS logs telltale messages: the kernel
for FIDO2, `usbsmartcardreaderd` for OpenPGP. `ykding` tails `log stream`
for those.

For commit context, it inspects the `ssh-keygen -Y sign` process git
spawned: its working directory gives the repo, and the message being signed
is read from the git dir (`rebase-merge/message`, `MERGE_MSG`, or
`COMMIT_EDITMSG`). All best-effort; if anything fails you get a plain
"Touch your YubiKey" banner.

## References

- [noperator/yknotify](https://github.com/noperator/yknotify): the
  detection heuristic (which log messages signal a touch request) is
  ported from here.

## License

[MIT](LICENSE)
