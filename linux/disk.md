# Disk tools

## How to find out usage stats

Using `df` to get information on all the filesystems currently mounted.

```
$> df
Filesystem     1K-blocks     Used Available Use% Mounted on
udev             5223032        4   5223028   1% /dev
tmpfs            1046752     9564   1037188   1% /run
/dev/sda1       36494144 26869268   7748140  78% /
none                   4        0         4   0% /sys/fs/cgroup
none                5120        0      5120   0% /run/lock
none             5233744   140956   5092788   3% /run/shm
none              102400       60    102340   1% /run/user
```
