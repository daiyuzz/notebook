# 解决ubuntu开机后键盘鼠标失灵

近期不知安装了什么package，导致 ubuntu 开机后键盘鼠标一直没法用，刚开始以为是 ubuntu 桌面环境崩溃了，后来发现系统能显示连接到网络、时间也在运行，那应该就是键盘鼠标失灵了。

网上查了一下，应该是 xserver-xorg-input-all 包被删除了，重新安装一下就可以了！方法如下：

1. 重启系统按 Esc 进入 grup 引导界面，选择 Advanced Option，再选择 recovery mode，按 Enter 进入

2. 选择 resume 进入，屏幕左上角会出现一直闪烁的光标

3. 按 Ctrl + Alt + F1 进入 tty 命令行界面，输入自己的用户名和密码

4. 输入 sudo apt install xserver-xorg-input-all （前提 ubuntu开机能自动连接到可以上网的网络，若不能，请搜索命令行连接网络的教程），安装完成后 reboot 重启即可！

 
