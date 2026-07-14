# ykding

Plays a sound and shows a notification whenever your YubiKey is waiting for
a touch on macOS. One ding = one touch owed.

Useful when the key is plugged in out of sight (iMac rear ports, docks). A
rebase that re-signs five commits dings after each touch until all five are
done.

## Install

```sh
brew install onyb/tap/ykding
brew services start ykding
```

Then `ykding test` plays both alerts so you know the sounds. If no banner
appears, allow notifications for **Script Editor** in System Settings →
Notifications.

ykding has zero dependencies. It only uses tools that ship with macOS
(`log`, `afplay`, `osascript`).

## What you get

- **Glass** ding + banner for FIDO2 touches (`sk-ssh-*` keys, git commit
  signing, SSH auth). Any FIDO2 security key triggers this, not just
  YubiKeys.
- **Ping** ding + banner for OpenPGP touches (gpg on the YubiKey).
- When git signing triggered the touch, the banner shows the repo and
  the commit subject:

  <img width="400" src="https://github.com/user-attachments/assets/512c5767-3f15-4a55-895f-e465c5697606" />


## Commands

| Command          | What it does                                    |
| ---------------- | ----------------------------------------------- |
| `ykding test`    | Play both alerts once                           |
| `ykding run`     | Run in the foreground (what brew services runs) |
| `ykding version` | Print the version                               |

The background service is managed by Homebrew: `brew services stop
ykding` to pause it, `brew upgrade ykding` to update. Logs go to
`$(brew --prefix)/var/log/ykding.log`, one line per touch request.

## How it works

While a YubiKey waits for a touch, macOS logs telltale messages: the kernel
for FIDO2, `usbsmartcardreaderd` for OpenPGP. `ykding` tails `log stream`
for those. The messages are a heuristic, not an API; tested on macOS 26
(Intel). Open an issue if your macOS logs differently.

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
