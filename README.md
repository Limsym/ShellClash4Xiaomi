# ShellClash用于小米AX3000T路由器

本文据多篇来源按图索骥实现小米AX3000T的科学上网（软固化）。多有遗漏不足，敬请斧正🙏

**背景：**我是一位Clash (Meta/Verge) 4 Windows/Android 使用者，刚自购路由器，期望其实现VPN，如此我可以减少在终端上开关VPN软件的操作。

**路径：**解锁SSH→连接SSH→安装和配置ShellClash

**关键词：**路由器，科学上网

## **1. 解锁SSH**

路由器主页没有关于SSH选项，故需借外力打通。

![](https://picx.zhimg.com/80/v2-0fe5362cd1838907cbbddf77ae23770a_720w.png)

登录路由器后台，复制自己的stok变量

打开Windows系统CMD ，最好以管理员身份运行下面代码： 依次输入以下代码，小米 AX3000T 解锁 SSH 命令：（整体替换为你浏览器地址栏看到的数值）

```
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20set%20ssh_en%3D1%0A"
```

```
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20commit%0A"
```

```
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Ased%20-i%20's%2Fchannel%3D.*%2Fchannel%3D%22debug%22%2Fg'%20%2Fetc%2Finit.d%2Fdropbear%0A"
```

```
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0A%2Fetc%2Finit.d%2Fdropbear%20start%0A"
```

![](https://picx.zhimg.com/80/v2-3241b262ea4d538144683b22d7e4f7e5_720w.png)

正常情况会出现code：0 就是解锁ssh成功了，没有固化ssh，升级系统后可能会丢失ssh连接

## **2. 连接SSH**

### **路径1**

**计算一下ssh的密码**：登录官方网址： https://miwifi.dev/ssh

输入路由器的SN可以得到SSH密码，路由器后台（及路由器机身背面）有显示SN号码。

在CMD里使用系统自带的命令：`ssh root@192.168.31.1`

> 如果返回拒绝连接，转用路径2的脚本https://github.com/openwrt-xiaomi/xmir-patcher/
> 

### 路径2

经过一番寻找，这个脚本https://github.com/openwrt-xiaomi/xmir-patcher/完美解决了解锁SSH的问题 。**赞美作者!**

脚本的项目地址如下 `https://github.com/openwrt-xiaomi/xmir-patcher`，当然，也可以点击后面的按钮直接下载。[点击下载xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher/archive/refs/heads/main.zip)

下载解压后点击run.bat，按2连接至设备，按提示操作，输入路由器后台管理页面的密码，等待一会就直接解锁了SSH。

> 之后也可以按7进入其他功能，使用**固化SSH功能**，这样就能避免以后连不上。SSH密码也可以**在其他功能里直接改**一个好记的。
> 

在CMD里使用系统自带的命令：`ssh root@192.168.31.1` 

> 如果提示不是内部或外部命令，也不是可运行的程序 或批处理文件 就是系统没有ssh命令，使用第三方工具连接，如putty（我没学会）。
> 

然后**输入密码（密码不可见），输完回车即可**。

> 如果密码总是错误，可回xmir直接重置为指定密码
> 

![image.png](attachment:b00093be-8ce1-4495-a0aa-3c97f6e6c849:image.png)

解锁成功截图

## 安装ShellClash

继续在cmd中使用curl安装：

`export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null`

按顺序选择对应数字:（以下顺序可能过时，但其思路仍可参考）

- 选2 shellclash稳定版，安装目录1或者2都可以
- 选1透明代理
- 选1 启用**软固化**
- 选1 导入配置文件，复制粘贴订阅链接，回车
- 选1 开始生成配置文件
- 选1 立即启动clash服务
- 选9 更新
- 选4 安装本地dashboard面板

安装完成后即可访问：http://192.168.31.1:9000/ui  管理相关规则

![image.png](attachment:ba1e8569-7767-490f-938f-7680f7dbdad6:image.png)

web dashboard (Meta基础面板)

---

## 参考：

1. https://mao.fan/article/102
2. https://www.fuling.me/posts/42627/
3. https://www.kejitt.top/24.html
4. https://www.youngwen.com/post/37
