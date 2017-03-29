---
layout:     post
title:      "让Linux定时自动执行清理任务 —— 基于Crontab"
subtitle:   "Let the Linux periodically execute cleanup tasks - based on Crontab"
date:       2017-03-28 17:001:00
author:     "Cux"
header-img: "img/post-bg-rwd.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Linux
    - shell
    - 定时清理
---



>  在服务端开发过程中，为了便于后续分析优化与问题排查，需要对关键流程进行日志记录。伴随着公司业务的发展，服务端的业务模块和代码逻辑变得越来越复杂，日志量也成倍增长，在硬盘空间有限的情况下，日志文件在数天内很快就会占满空间导致服务性能下降、甚至于服务器宕机。定期手动清理日志是一件非常繁琐的事情，因此我们需要使用任务来定期清理无用日志。


## 命令
    0 1 * * * find /apps/shihui_engine_service/logs -atime +7 -exec rm -f {} \;


这个指令的含义是：每天凌晨1点0分执行，删除 /apps/shihui_engine_service/logs 目录下更新时间超过7天的所有文件。

这个命令的具体含义是什么？

- 前5位表示时间和频率，具体含义如下

![](/img/in-post/20140409140602.jpg)
*时间字段解释*

在以上各个字段中，还可以使用以下特殊字符：

1. 星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
2. 逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
3. 中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
4. 正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。




举个栗子：

    30 21 * * * /usr/local/etc/rc.d/lighttpd restart 
表示每晚的21:30重启apache。 

    0,30 18-23 * * * /usr/local/etc/rc.d/lighttpd restart 
表示在每天18 : 00至23 : 00之间每隔30分钟重启apache。 

    0 23-7/1 * * * /usr/local/etc/rc.d/lighttpd restar
晚上11点到早上7点之间，每隔一小时重启apache 

组合挺多，有兴趣的可以慢慢研究。

# find #
查找命令
find [-path......] -options [-print -exec -ok]

path:要查找的目录路径（“~”表示$home目录；“.”表示当前目录；“/”表示根目录）。

print：将结果输出到标准输出。

exec：对匹配的文件执行该参数所给出的shell命令，形式为：command {} \,（{}和\,之间有空格）。

ok：与exec作用相同，区别是在执行命令之前，都会给用户提示，让用户确认是否执行。

***+7是7天以前的，-7就是7天以内。***

-atime +7的意思是：7天以前访问过的文件。

**注：atime、mitme均可，mtime对应文件内容的修改，atime在文件查看情况下也会更新。**

如果自动执行shell脚本，步骤如下：

1，创建shell脚本

2，修改shell脚本的执行权限 : chmod +x test.sh

3， 在/etc/crontab文件后添加定时任务,如上面（1），（2）

4， 最后重启crontab服务让新配置的定时任务生效，service crond restart　　


如果直接执行命令比，步骤如下：

1，在/etc/crontab文件后添加定时任务。比如上述代码。

2， 最后重启crontab服务让新配置的定时任务生效，service crond restart　　



	


