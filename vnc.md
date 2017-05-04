# [Go to home page] [1]
<center>2017-5-4</center><br>

## 1.首先执行这一句防止系统文件被修改
```markdown
chattr +i /etc/resolv.conf
```
## 2、然后安装tigervnc
```markdown
sudo yum install -y tigervnc tigervnc-server
```
## 3.查看自己的服务器支持安装哪些包
```markdown
sudo yum grouplist
```
###### 查看自己的服务器里中Available Environment Groups下面有哪些可以安装的Desktop,我这里的是GNOME Desktop
## 4. 安装GNOME Desktop
```markdown
sudo yum groupinstall GNOME Desktop
```

## 5. 启动服务
```markdown
vncserver
```
## 6.连接vnc 
>第一次执行会提示输入密码，然后再验证输入一次回车，vnc服务端就算搭建好了！
接下来在手机或者电脑上下载vnc客户端，输入你的IP:5901连接
然后输入密码就可以看到你的服务器界面了！

<div style="height:200px;widh=100%;">
<center>Author：Chanji</center>
<center>转载请与作者联系</center>
<center>邮箱：1138552873@qq.com</center>
</div>

[1]: https://jchanji.github.io "主页"