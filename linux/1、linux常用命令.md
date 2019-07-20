```bash
#1.查看机器最后开机时间
date -d "$(awk -F. '{print $1}' /proc/uptime) second ago" +"%Y-%m-%d %H:%M:%S"

who -r		#或者这个

#2.查看系统运行多长时间
uptime -p
```

