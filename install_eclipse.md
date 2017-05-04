# [Go to home page] [1]
<center>2017-5-4</center><br>
#### 操作系统：CentOS 7
#### eclispe版本：Eclipse Mars 2
## 下载安装
> ### 1.下载安装[eclipse][2]

> ### 2.解压 

```markdown
 sudo tar -zxvf [下载的安装包名称] -C [安装的目录]  
```

> ### 3.创建软链接

```markdown
 sudo ln -s /安装的目录/eclipse/eclipse  /usr/bin/eclipse
```

> ### 4.添加图标

```markdown
gedit /usr/share/applications/eclipse.desktop
```
将下面内容添加到文件中
```mrkdown
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse Mar2
Exec=/usr/bin/eclipse
Icon=/[解压的目录]/eclipse/icon.xpm
Categories=Application;Development;Java;IDE
Version=1.0
Type=Application
Terminal=0
```

<div style="height:200px;widh=100%;">
<center>Author：Chanji</center>
<center>转载请与作者联系</center>
<center>邮箱：1138552873@qq.com</center>
</div>










[1]: https://jchanji.github.io "主页"
[2]: http://mirrors.ustc.edu.cn/eclipse/technology/epp/downloads/release/mars/2/eclipse-jee-mars-2-linux-gtk-x86_64.tar.gz "eclise下载"