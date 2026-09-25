# himalaya-gmail-setup

One-command setup for reading and sending Gmail from the terminal with
[himalaya](https://github.com/pimalaya/himalaya) v2.x. Made for setting up new
machines quickly and consistently.

## What it does

1. Installs the `himalaya` binary if it's missing (to `~/.local`, no root needed)
2. Prompts for your Gmail address, display name, and Gmail **App Password**
   (typed into a hidden prompt — never echoed)
3. Writes the himalaya config and the app-password file (both `chmod 600`)
4. Verifies the connection with `himalaya account check`

## Prerequisites

- Linux or macOS with `bash`
- A Google account with **2-Step Verification** enabled
- A **Gmail App Password** — get one at:

  **https://myaccount.google.com/apppasswords**

  (Google Account → Security → 2-Step Verification → App passwords. If the
  link bounces you to security settings, enable 2-Step Verification first.
  The password is 16 letters in 4 groups, like `abcd efgh ijkl mnop`. You can
  generate a fresh one at any time.)

## Quick start

Download `himalaya-gmail-setup.sh` from this repo (green **Code** button →
Download ZIP, or clone), then run it:

```sh
bash himalaya-gmail-setup.sh
```

Or fetch the raw script directly (replace `OWNER` with this repository's owner):

```sh
curl -sSL https://raw.githubusercontent.com/OWNER/himalaya-gmail-setup/main/himalaya-gmail-setup.sh -o himalaya-gmail-setup.sh
bash himalaya-gmail-setup.sh
```

If you leave the app-password prompt blank, the script tells you how to add it
to the file later and skips the connection check.

## Files written

| File | Purpose |
|---|---|
| `~/.config/himalaya/config.toml` | himalaya v2 config (IMAP + SMTP, Gmail mailbox aliases) |
| `~/.config/himalaya/.app-password` | the Gmail App Password, `chmod 600`, read by himalaya via a `cat` command |

An existing `config.toml` is backed up as `config.toml.bak.<timestamp>` before
being replaced. If `XDG_CONFIG_HOME` is set, its value is used instead of
`~/.config`.

## After setup

```sh
himalaya account check      # verify IMAP + SMTP
himalaya envelope list      # list the inbox
himalaya message compose --to someone@example.com --subject Hi --body "Hello" --send
```

More commands: [himalaya docs](https://github.com/pimalaya/himalaya#usage).

## Troubleshooting

- **`imap: FAIL` / `smtp: FAIL` with "Invalid credentials" or 535** — wrong or
  expired app password, or 2-Step Verification is not enabled. Generate a fresh
  app password at https://myaccount.google.com/apppasswords and overwrite the
  secret file:
  `printf '%s' 'NEW-APP-PASSWORD' > ~/.config/himalaya/.app-password && chmod 600 ~/.config/himalaya/.app-password`

- **`No backend matching 'auto' is configured for this account`** — the config
  is using himalaya **v1** keys (`backend.*`, `message.send.backend.*`,
  `folder.aliases.*`). Himalaya v2 silently ignores them. Re-run this script to
  regenerate a v2 config.

- **`account check` reports FAIL but the exit code is 0** — known himalaya v2
  behavior; judge by its output, not the exit code (this script already does).

## Security notes

- The app password is read from a hidden prompt and written only to
  `~/.config/himalaya/.app-password` (mode 600). It is never echoed.
- If the app password ever leaks (chat logs, screenshots, etc.), delete it at
  https://myaccount.google.com/apppasswords and generate a fresh one.
- Both written files are mode 600. Nothing is transmitted anywhere except the
  normal Gmail IMAP/SMTP authentication.
