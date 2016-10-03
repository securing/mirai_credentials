# Mirai botnet credentials

The Mirai botnet abuses hardcoded by manufacturers of devices root credentials for undocumented telnet service.

There are [advisories](https://krebsonsecurity.com/2016/10/who-makes-the-iot-things-under-attack/comment-page-1/) suggesting that in order to avoid rapidly being reinfected, you should change your default web interface password.
However, UI password for users are stored independently, and changing user credentials does not affect OS-level telnet root password.
In order to prevent reinfection, you should change the hardcoded root telnet password, or disable telnet access.

Following command can help you find out whether your device may be affected:

Usage:
```
$ hydra -C mirai_creds.txt telnet://X.X.X.X
Hydra v8.1 (c) 2014 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (http://www.thc.org/thc-hydra) starting at 2016-10-04 01:07:44
[WARNING] telnet is by its nature unreliable to analyze, if possible better choose FTP, SSH, etc. if available
[DATA] max 16 tasks per 1 server, overall 64 tasks, 62 login tries, ~0 tries per task
[DATA] attacking service telnet on port 23
[23][telnet] host: X.X.X.X   login: root   password: xmhdipc
1 of 1 target successfully completed, 1 valid password found
Hydra (http://www.thc.org/thc-hydra) finished at 2016-10-04 01:08:00
```

The root credentials are hardcoded and not easy to change:

```
$ telnet X.X.X.X
Trying X.X.X.X...
Connected to X.X.X.X.
Escape character is '^]'.
LocalHost login: root
Password: 
Welcome to Monitor Tech.
# passwd 
-sh: passwd: not found
# cat /etc/passwd
root:$1$RYIwEiRA$d5iRRVQ5ZeRTrJwGjRy.B0:0:0:root:/:/bin/sh
# touch /etc/passwd
touch: /etc/passwd: Read-only file system
```
(remount -o rw does not work)

# temporary solution - until next reboot ;)

```
# killall telnetd
Connection closed by foreign host.
```

If possible, block it on firewall.

# persistent solution

One of the options is to reflash firmware image.
Details coming soon...

# Source

https://github.com/jgamblin/Mirai-Source-Code)
```
grep "auth_entry(\"" mirai/bot/scanner.c | sed -e "s/.*\/\/\ \([^ ]*\) *\([^ ]*\)/\1:\2/g" | sed -e "s/(none)//g" > mirai_creds.txt
```
