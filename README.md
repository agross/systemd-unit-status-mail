# systemd-unit-status-mail

Be notified via e-mail when a systemd unit enters failed state. Based on
[this blog post](http://northernlightlabs.se/systemd.status.mail.on.unit.failure).

## Installation

1. Decide where you want to install things.

   ```sh
   INSTALL_DIR=/usr/local/src/systemd-unit-status-mail
   ```

1. Clone this repository.

   ```sh
   $ git clone https://github.com/agross/systemd-unit-status-mail.git "$INSTALL_DIR"
   Cloning into 'systemd-unit-status-mail'...
   ```

1. Install the systemd unit.

   ```sh
   $ "$INSTALL_DIR/install"
   Linking /usr/local/src/systemd-unit-status-mail/unit-status-mail@.service
   Linking /usr/local/src/systemd-unit-status-mail/unit-status-mail
   ```

1. Tell `unit-status-mail@.service` who should receive notifications.

   ```sh
   $ systemctl edit unit-status-mail@.service

   # Editor opens. Add these lines and save the file.

   [Service]
   Environment=MAILTO=adam@example.com,eve@example.com
   ```

1. Configure units that you want to be notified about.

   ```sh
   $ systemctl edit some.service

   # Editor opens. Add these lines and save the file.

   [Unit]
   OnFailure=unit-status-mail@%n.service
   ```

## Optional configuration items to think about

### Test your setup

```sh
$ UNIT=auditd.service
$ systemctl start unit-status-mail@$UNIT.service
$ systemctl status unit-status-mail@$UNIT.service
● unit-status-mail@auditd.service.service - Unit Status Mailer Service
   Loaded: loaded (/usr/local/src/systemd-unit-status-mail/unit-status-mail@.service; linked; vendor preset: disabled)
  Drop-In: /etc/systemd/system/unit-status-mail@.service.d
           └─override.conf
   Active: inactive (dead)

Jan 22 18:45:11 fedja.wghoch4.local systemd[1]: Starting Unit Status Mailer Service...
Jan 22 18:45:11 fedja.wghoch4.local unit-status-mail[13876]: <13>Jan 22 18:45:11 /etc/systemd/system/unit-status-mail: Status mail sent to adam@example.com,eve@example.com for unit auditd.service
Jan 22 18:45:13 fedja.wghoch4.local systemd[1]: Started Unit Status Mailer Service.
```

### Getting the whole picture

To get all `unit-status-mail@.service` settings use

* `systemctl cat unit-status-mail@.service` and
* `systemctl show unit-status-mail@it-does-not-matter.service`.
