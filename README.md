## 3月16日更新的内容：
1. 随机启动的问题在 [@Jacky Bao](https://plus.google.com/u/0/108855478531549571757/posts/DabLxwHAwGH) 的分享下顺利解决
2. 进入uml的方式更简单，只要执行screen -x或者bash run.sh status即可
3. 在@allient neko的帮助下加入了母机和uml的文件交换目录，可以方便的互传文件

安装包还有问题可以回帖告知，感谢大家的测试反馈~


只是测试版，欢迎测试。
1. 随机启动的问题在 [@Jacky Bao](https://plus.google.com/u/0/108855478531549571757/posts/DabLxwHAwGH) 的分享下顺利解决
2. 使用alpine linux，启动只需64M内存，已在我自己的128M内存的小鸡上测试通过
3. 已自带SSR，相关的SSR说明在后面。感谢@allient neko加入了ssr功能
4. 感谢 [@Jacky Bao](https://plus.google.com/u/0/108855478531549571757/posts/DabLxwHAwGH) 提供的原包,方便小内存运行
5. 建议大家看手动安装的文章，加深理解。[文章点我](https://www.91yun.org/archives/4996)
6. UML毕竟不是系统，甚至不是虚拟机，不要抱太大指望，不要有太多想法。能运行起来SSR我感觉已经很不错啦


# 前置准备
请选择安装`64bit`的操作系统，目前还没做32bit的包
VPS必须支持并开启TUN/TAP

# 一键运行脚本
```
wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/uml/master/bbr/uml.sh && bash uml.sh
```
装完ssr和bbr就已经运行了，如果你不想改密码和端口的话就可以直接用。。默认配置在下面。。
# UML相关说明
## 如何进入uml
`screen -x`或者在目录下运行`bash run.sh status`
用户名：`root` ， 密码：`root`
## 如何退出uml
在uml模式里，通过按快捷键`ctrl+a+d`来退出screen
## 删除uml进程
```
bash run.sh stop
```
## 启动uml
```
bash  run.sh start
```
## 如何把文件传入UML
我已经在root目录下新建了个umlshare目录（`/root/umlshare`）,在uml里也做了挂载，位置同样是`/root/umlshare`,只要把文件放进去就可以了
# SSR相关说明
## 默认配置
```
端口：9191
密码：www.91yun.org
加密：RC4-MD5
协议：auth_sha1_v4
混淆：tls1.2_ticket_auth
```
## 如何修改SSR配置
### 第一步
根据上面的教程进入uml
### 第二步
修改mudb.json文件
```
vi /root/shadowsocksr/mudb.json
```
把相关的配置改成你自己需要的就可以了。需要说明的是，因为没有编译相关的依赖库，不支持chacha20的加密方法
### 第三步
重启SSR程序
```
bash /root/shadowsocksr/run.sh
```
### 最后一步
在母机的安装目录下打开run.sh找到下面的代码，把9191改成你自己刚才修改的端口
```
iptables -t nat -A PREROUTING -i venet0 -p tcp --dport 9191 -j DNAT --to-destination 10.0.0.2
iptables -t nat -A PREROUTING -i venet0 -p udp --dport 9191 -j DNAT --to-destination 10.0.0.2
```
然后重启即可

## SSR添加多用户
```
python mujson_mgr.py -a -p 端口 -k 密码 -m 加密方式 -O 协议 -o 混淆
```
加完记得在母机增加iptables端口转发相关的代码





# OpenVZ使用BBR新姿势：LKL一键安装包
感谢 @allientNeko 的研究。具体帖子请看： https://www.91yunbbs.com/discussion/87
简单的说 LKL 相比 uml 好处还是不少的，只需要转发端口，无论是 ssr 都是装在母鸡上，和你平时使用无差，而 UML 需要在 UML 系统里安装。 LKL 的占用资源也更少。不过据说 LKL 的加速效果没有 UML 好，我没有测试验证，大家可以自行折腾。

## LKL 使用前置需求
LKL 要求 ldd 的版本至少在 2.14 ，目前我测试下来，如果不想折腾建议直接安装 CentOS7 ， Debian8 和 Ubuntu16
安装包只使用 64bit 的系统。
默认的端口转发只转发了 9000-9999 的端口，如果你不想费心修改，请把 ssr 等应用的端口设在这个范围
只适用 openvz ，请他虚拟请参考 原帖 自己折腾。


## 安装 LKL 一键包命令
```
wget --no-check-certificate https://github.com/91yun/uml/raw/master/lkl/install.sh && bash install.sh
```


## 如何判断是否安装成功
```
ping 10.0.0.2
```
如果 10.0.0.2 能 ping 通说明成功， ping 不通说明失败


## 如果修改转发端口
```
修改 /root/lkl/run.sh ，查找 9000-9999 ，改成你想要的端口段
修改 /root/lkl/haproxy.cfg 查找 9000-9999 ，改成你想要的端口段
重启 VPS
```

