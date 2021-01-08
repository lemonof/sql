##### 打印日志

tail -f /opt/IBM/WebSphere/AppServer/profiles/AppSrv01/logs/server1/SystemOut.log

##### 重启was

/opt/IBM/WebSphere/AppServer/profiles/AppSrv01/temp  --应用历史临时目录

/opt/IBM/WebSphere/AppServer/profiles/AppSrv01/bin   --01was

/opt/IBM/WebSphere/AppServer/bin    --总的was

sh stopServer.sh server1

sh startServer.sh server1

ps -ef|grep server1

kill -9 123123(进程号)   -9 直接杀死  -15等待进程结束后，再杀死

##### 配置was进程挂起

服务器-->服务器类型-->WebSphere Application Server

通信-->服务器基础结构-->管理-->定制服务

名称：com.ibm.websphere.threadmonitor.interval

值：0





