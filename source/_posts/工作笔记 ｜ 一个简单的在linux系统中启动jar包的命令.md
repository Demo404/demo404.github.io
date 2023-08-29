---
title: 工作笔记 ｜ 一个简单的在linux系统中启动jar包的命令
date: 2023-08-29 22:32:50
tags: ['Shell']
---

> 当有多个项目时，挨个重启总是感觉很麻烦，所以下面这个脚本诞生了，直接替换app列表中的参数为启动的jar包名即可挨个启动项目。
> 启动前会判断当前是否存在运行该jar包的进程，存在的话先杀死进程，再执行启动命令。
> 日志输出可以提出来写一个公共方法，启动模版也是（后面有时间的话可以考虑可以改一下）。

```bash
#!/bin/bash

source ~/.bash_profile
source /etc/profile

echo "#####################  "`date '+%Y-%m-%d %H:%M'`" ########################" >> run.log

###JVM参数
JAVA_OPTIONS="-Xms1024m -Xmx1024m -Xmn256m -Xss256k -XX:MetaspaceSize=64m -XX:MaxMetaspaceSize=64m"

### JMX参数
JMX_OPTIONS="-Djava.rmi.server.hostname=127.0.0.1 -Dcom.sun.management.jmxremote.port=8098 -Dcom.sun.management.jmxremote.rmi.port=8098 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false"

# 服务列表
app=(linze_healthcheck_java_pc-0.0.1-SNAPSHOT)
for i in ${app[*]};do
    
    # 停止旧项目
    ps -ef | grep $i.jar | grep -v grep | awk '{print $2}' | xargs kill -9
    echo '停止项目完成!!!'

	pid=`ps -ef | grep $i.jar| grep -v grep | awk '{print $2}'`
	# 判断当前服务是否启动
	if [ "$pid" != "" ]; then
		echo '当前服务[ '$i' ]已经是运行状态,端口号：'$pid >> run.log
		# 杀死进程重新启动
		echo '杀死旧进程:'$pid', 准备重新启动' >> run.log
		kill -9 $pid
		# 休息3s
		sleep 3
		for name in `find . -name $i.jar`;do
			nohup java -jar $JAVA_OPTIONS $JMX_OPTIONS $name > /dev/null 2>&1 &
			sleep 3
			ppid=`ps -ef | grep $i.jar| grep -v grep | awk '{print $2}'`
			if [ "$ppid" != "" ]; then
				echo '服务[ '$i' ]重启完成！' >> run.log
			else
				echo '服务[ '$i' ]重启失败！' >> run.log
			fi
		done
	else
		for name in `find . -name $i.jar`;do
			echo '当前启动服务[ '$i' ], jar位置:'$name >> run.log
			nohup java -jar $JAVA_OPTIONS $JMX_OPTIONS $name > /dev/null 2>&1 &
			sleep 3
			ppid=`ps -ef | grep $i.jar| grep -v grep | awk '{print $2}'`
			if [ "$ppid" != "" ]; then
		        echo '服务[ '$i' ]启动完成！' >> run.log
			else
				echo '服务[ '$i' ]启动失败！' >> run.log
			fi
		done
	fi
done
```