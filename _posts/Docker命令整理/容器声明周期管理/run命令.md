<font size=8 >run</font>
<br/>
<br/>
<font size=4>**创建一个新的容器并执行命令**</font>
<br/>
<br/>
<code> docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]<br/>
 -a: stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项<br/>
-c, --cpu-shares=0: CPU共享（相对权重）<br/>
-d: 后台运行容器，并返回容器ID<br/>
-e, --env=[]: 设置变量环境<br/>
-h, --host="": 指定容器的hostname<br/>
-i, --interactive=false: 以交互模式运行容器，通常与 -t 同时使用<br/>
-n, --networking=true: 启用容器的网络<br/>
-p, --publish=[]: 将本地端口和容器端口进行映射<br/>
-P, --publish-all=false: 将所有暴露的端口映射到主机端口<br/>
--rm=false: 当容器执行完退出的时候自动删除容器 (不能和-d一起使用)<br/>
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用<br/>
--dns=[]: 指定容器使用的DNS服务器，默认和宿主一致<br/>
--link=[]: 添加链接到另一个容器<br/>
--expose=[]: 开放一个端口或一组端口<br/>
</code>
   	
	
    
    
    
    
    
    
	 
     




  