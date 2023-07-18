## systemd-timesyncd

### configuration

configuration file is */etc/systemd/timesyncd.conf*

```shell
[Time]
#NTP=
#FallbackNTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org
#...
```

To verify your configuration

```shell
timedatectl show-timesync --all
```

### Usage

To enable and start it, run

```shell
timedatectl set-ntp true
```

To see verbose service information

```shell
timedatectl timesync-status
```