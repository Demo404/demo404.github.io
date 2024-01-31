---
title: 脚本/Jar包启动、停止、重启脚本
date: 2024-01-31 11:29:15
tags:
---

```shell
#APP_ID MUST BE UNIQUE.
APP_ID="DATA_GENERATE"
JAR_FILE_NAME="data_generate.jar"
SERVER_PORT="10002"

JAVA=/home/weblogic/java-se-8u40-ri/bin/java
echo JAVA_HOME=$JAVA_HOME

######set properties to override yaml#####
ISA_SYS_PARAM="--spring.profiles.active=prod"
ISA_SYS_PARAM="${ISA_SYS_PARAM} --server.port=${SERVER_PORT}"
ISA_SYS_PARAM="${ISA_SYS_PARAM} --logging.loglevel=INFO"

######JVM Options######
JAVA_OPT="-Xmx2g  -Xms1g -XX:MaxPermSize=128m "
JAVA_OPT="${JAVA_OPT} -XX:HeapDumpPath=./jvmdump/${SERVER_PORT}_dumpfile.hprof "
JAVA_OPT="${JAVA_OPT} -XX:+HeapDumpOnOutOfMemoryError "

#ONE JAR MORE INSTANCE
PID=$(ps -ef|grep DPGM_ID=${APP_ID} | grep -v grep|awk '{print $2}')
#ONE JAR ONE INSTANCE
#PID=$(fuser ${JAR_FILE_NAME} 2>/dev/null | xargs echo)


start(){
  if ! kill -0 ${PID} 2>/dev/null ;then
  	echo "${APP_ID} is starting! File is ${JAR_FILE_NAME}"
    nohup ${JAVA} -jar ${JAVA_OPT} -DPGM_ID=${APP_ID} -DAPP_NAME=${APP_ID} ${JAR_FILE_NAME} ${ISA_SYS_PARAM} &
  else
    echo "Failed to start '${APP_ID}',because it is running (pid:${PID})."
  fi

}


stop(){

	 if  ! kill -0 ${PID} 2>/dev/null; then
	     echo "Failed to stop '${APP_ID}',It is not running."
	  else
	    kill ${PID}
	     echo "kill ${PID},${APP_ID} is killing."
	    a=0
	    until  ! kill -0 ${PID} 2>/dev/null
	    do

	      if [ $a -lt 10 ] ;then

	        echo "Stopping '${APP_ID}', Recheck 3 seconds later."
	        sleep 3
	      else
	        break
	      fi
	      a=`expr $a + 1`
	    done
	  fi
	  if  kill -0 ${PID} 2>/dev/null  ;then
	     echo "Fail to kill '${APP_ID}' in 30 seconds."
	     kill -9 ${PID}
	     echo "kill -9 ${PID}, ${APP_ID} is killed"
	  fi
	  sleep 3
	  if  ! kill -0 ${PID} 2>/dev/null  ;then
	    echo "'${APP_ID}' has been stoped."
	  else
	    echo "'${APP_ID}' fail to stop."
	  fi

}

restart(){
    stop
    start
}

status(){
  if kill -0 ${PID} 2>/dev/null ;then
    echo "${APP_ID} (pid:${PID}) is running."
  else
    echo "${APP_ID} is not running."
  fi
}

case $1 in
  start)
    start;;
  stop)
    stop;;
  restart)
    restart;;
  fstop)
    fstop;;
  status)
    status;;
  *)
    echo "Usage: $0 start|stop|status"
esac
```