[use-systemd-timers](https://www.certdepot.net/rhel7-use-systemd-timers)
[Рow-to-set-timer](https://tutorials.tinkink.net/en/linux/how-to-set-timer-with-systemd.html)
```
Minimal form                   Normalized form
Sat,Thu,Mon-Wed,Sat-Sun    ==> Mon-Thu,Sat,Sun *-*-* 00:00:00
Mon,Sun 12-*-* 2,1:23      ==> Mon,Sun 2012-*-* 01,02:23:00
Wed *-1                    ==> Wed *-*-01 00:00:00
Wed-Wed,Wed *-1            ==> Wed *-*-01 00:00:00
Wed, 17:48                 ==> Wed *-*-* 17:48:00
Wed-Sat,Tue 12-10-15 1:2:3 ==> Tue-Sat 2012-10-15 01:02:03
*-*-7 0:0:0                ==> *-*-07 00:00:00
10-15                      ==> *-10-15 00:00:00
monday *-12-* 17:00        ==> Mon *-12-* 17:00:00
Mon,Fri *-*-3,1,2 *:30:45  ==> Mon,Fri *-*-01,02,03 *:30:45
12,14,13,12:20,10,30       ==> *-*-* 12,13,14:10,20,30:00
mon,fri *-1/2-1,3 *:30:45  ==> Mon,Fri *-01/2-01,03 *:30:45
03-05 08:05:40             ==> *-03-05 08:05:40
08:05:40                   ==> *-*-* 08:05:40
05:40                      ==> *-*-* 05:40:00
Sat,Sun 12-05 08:05:40     ==> Sat,Sun *-12-05 08:05:40
Sat,Sun 08:05:40           ==> Sat,Sun *-*-* 08:05:40
2003-03-05 05:40           ==> 2003-03-05 05:40:00
2003-03-05                 ==> 2003-03-05 00:00:00
03-05                      ==> *-03-05 00:00:00
hourly                     ==> *-*-* *:00:00
daily                      ==> *-*-* 00:00:00
monthly                    ==> *-*-01 00:00:00
weekly                     ==> Mon *-*-* 00:00:00
*:20/15                    ==> *-*-* *:20/15:00
```
