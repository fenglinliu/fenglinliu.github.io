<font size=8 >commit</font>
<br/>
<br/>
<font size=4>**从容器创建一个新的镜像**</font>
<br/>
<br/>
<code> docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]<br/>
-a, --author="": 提交的镜像作者<br/>
-c: 使用Dockerfile指令来创建镜像<br/>
-m, --message="": 提交时的说明文字<br/>
-p: 在commit时，将容器暂停<br/>
--run="": 当镜像用docker run命令启动时，使用该命令设定的配置<br/>
(ex: -run='{"Cmd": ["cat", "/world"], "PortSpecs": ["22"]}')
</code>
   	
	
    
    
    
    
    
    
	 
     




  