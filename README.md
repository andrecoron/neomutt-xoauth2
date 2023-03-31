# Why, god why?

something something sEcUrItY, something something

If your university or workplace hosts emails on services that need oauth-v2,
if you can't obtain a client_id and client_secret,
if you use (neo)mutt,
you've come to the right place.


# DISCLAIMER

Make sure you don't break any rules set up by your workplace/university.
I have no responsibilities whatsoever on you getting fired/kicked out of uni.
If you follow this guide and something goes bad you are on your own.


# <u>Prerequisites</u>:

- thunderbird/icedove
- (neo)mutt (if you are new to this use mutt-wizard to set it up)
- gpg
- pass (the standard unix password-manager)
- mutt_oauth2.py
- cyrus-sasl
- cyrus-sasl-xoauth2


# <u>Installation guide</u>

Bare in mind that i tested this process on Arch only.
  Let me know if you find any issue with it
  or if you test it and it works on other distros.

Depending on your distro you'll need different commands to obtain
and install those packages.

Arch
```sh
# pacman -Syu thunderbird neomutt gpg pass cyrus-sasl
$ paru -Syu mutt-wizard cyrus-sasl-xoauth2-git
```

Debian
```sh
# apt update && apt upgrade
# apt install thunderbird neomutt gpg pass cyrus-sasl git
$ git clone https://github.com/moriyoshi/cyrus-sasl-xoauth2
$ cd cyrus-sasl-xoauth2
$ ./autogen.sh
$ ./configure
$ make
# make install
```

Fedora
```sh
# dnf upgrade
# dnf install thunderbird neomutt gpg pass cyrus-sasl git
$ git clone https://github.com/moriyoshi/cyrus-sasl-xoauth2
$ cd cyrus-sasl-xoauth2
$ ./autogen.sh
$ ./configure
$ make
# make install
```

Gentoo
```sh
# emaint --auto sync
# emerge-webrsync
# emerge --ask --verbose mail-client/thunderbird mail-client/neomutt\
  app-crypt/gnupg app-admin/pass dev-libs/cyrus-sasl dev-vcs/git
$ git clone https://github.com/moriyoshi/cyrus-sasl-xoauth2
$ cd cyrus-sasl-xoauth2
$ ./autogen.sh
$ ./configure
$ make
# make install
```

OpenSUSE
```sh
### pick one:
# zypper patch
### or
# zypper update
# zypper install thunderbird neomutt gpg pass cyrus-sasl git
$ git clone https://github.com/moriyoshi/cyrus-sasl-xoauth2
$ cd cyrus-sasl-xoauth2
$ ./autogen.sh
$ ./configure
$ make
# make install
```


# <u>Configuration guide</u>

Now that we have installed everything we need let's set everything up.

- If you haven't already, set up your gpg keyring as in [this](https://wiki.archlinux.org/title/GnuPG) or [this](https://wiki.gentoo.org/wiki/GnuPG) guide

- Open thunderbird/icedove and login with your work/uni account.

- Check [here](https://hg.mozilla.org/comm-central/file/tip/mailnews/base/src/OAuth2Providers.jsm) your client_id and (if it's there) your client_secret.

- Open mutt_oauth2.py and add your gpg identity (the email to be clear)
  inside the empty "" in ENCRYPTION_PIPE,
  add your client_id and if known your client_secret in the corresponding lines.

- if your registrar is not one of google or microsoft you could try adding the
  necessary registration infos in a new list inside this python script filling it
  with the details you can find [here](https://hg.mozilla.org/comm-central/file/tip/mailnews/base/src/OAuth2Providers.jsm)

- Make mutt_oauth2.py executable and run it, follow the instructions and don't
  give up if it doesn't work the first time. You may need to select a different
  "preferred Oauth2 flow". Chose a TOKEN_FILENAME that makes sense.
```sh
$ chmod +x mutt_oauth2.py
$ ./mutt_oauth2.py TOKEN_FILENAME --verbose --authorize
```

- Edit your mutt configuration file. If you used mutt-wizard you'll find the account-specific
  configuration file in ~/.config/mutt/accounts/EMAIL_ACCOUNT.
  Add the following:
```lua
set imap_authenticators = "xoauth2"
set imap_oauth_refresh_command = "/some/local/path/mutt_oauth2.py /some/local/path/TOKEN_FILENAME"
set smtp_authenticators = ${imap_authenticators}
set smtp_oauth_refresh_command = ${imap_oauth_refresh_command}
```

- Edit your ~/.mbsyncrc accordingly in the corresponding email section
  PassCmd "/some/local/path/mutt_oauth2.py /some/local/path/TOKEN_FILENAME"
  AuthMechs XOAUTH2

- Open (neo)mutt and see if it works.


# Does it work?

Enjoy your favorite TUI email client.


# Doesn't it?

Double check every step of the guide, make sure there aren't any typos in your
config files and in the commands you wrote in your shell.
If there wasn't anything wrong open an Issue here on github making sure to
attach any config file you edited **STRIP IT OF ANY PERSONAL INFORMATION FIRST**
and your shell history with anything related to this guide.
Again **STRIP IT OF ANY PERSONAL INFORMATION FIRST**


# This guide was written thanks to:

- Alexander Perlis and his [mutt_oauth2](https://gitlab.com/muttmua/mutt/-/blob/master/contrib/mutt_oauth2.py) script
- Geoff Lankow's [Oauth2Providers.jsm](https://hg.mozilla.org/comm-central/file/tip/mailnews/base/src/OAuth2Providers.jsm)
- Luke Smith's [mutt-wizard](https://muttwizard.com/)
- Mbsync's [man-page](https://manpages.org/mbsync)
- [Neovim](https://neovim.io/)
- [Neorg](https://github.com/nvim-neorg/neorg), the true, native, Org-mode-like system for Neovim
- Peter van Ormondt's [guide](https://www.vanormondt.net/~peter/blog/2021-03-16-mutt-office365-mfa.html) on neomutt and office365
- [This](https://github.com/UvA-FNWI/M365-IMAP) repo that made me discover Oauth2Providers above
