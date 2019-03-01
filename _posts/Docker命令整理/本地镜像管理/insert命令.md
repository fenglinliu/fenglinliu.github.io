<font size=8 >insert</font>
<br/>
<br/>
<font size=4>**在指定路径的镜像中插入一个来自指定URL的文件**</font>
<br/>
<br/>
<code> docker insert IMAGE URL PATH<br/>
说明：加了包含文件的一层后会生成一个新的镜像，这个镜像是源镜像的子镜像。<br/>insert命令不会修改源镜像，新镜像的内容等于父镜像的所有内容加上插入的文件。<br/>
ex:docker insert 8283e18b24bc https://raw.github.com/metalivedev/django /tmp/postinstall.sh
</code>
   	
	
    
    
    
    
    
    
	 
     




  