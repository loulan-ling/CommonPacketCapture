## 常用的抓包方式



## Burp suite抓包

### 1. Burpsuite 抓WEB HTTPS包

​	**抓包工具 Burpsuite 2023**

​	**浏览器：Firefox**

**点击Burp环境配置**

![image-20230911181249985](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120913430.png)

**设置用来抓包的端口**

![image-20230911181347665](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120913751.png)

**默认抓包方式即可**

![image-20230911181519382](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120913110.png)

**打开浏览器设置端口代理跟burpsuite抓包的一样，然后访问`http://burpsuite`**

![image-20230911181738635](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120913498.png)

![image-20230911181758283](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120913758.png)

**点击右边`CA Certificate`下载证书**

**打开`firefox`浏览器设置，找到`View certificates`**

![image-20230911182138224](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914923.png)

**导入证书**

![image-20230911182256061](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914317.png)

**导入证书后复选第一个**

![image-20230911182627796](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914077.png)



**现在可以抓取https包了**



**删除证书**

**删除证书只需要在`Authorities`中找到`PortSwigger CA`删除即可**

![image-20230911182508722](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914084.png)



**Chrome抓包**

**访问`http://burpsuite/`下载CA证书**

![image-20230911183757471](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914109.png)

**打开chrome设置访问`隐私和安全`**

**在`安全`设置找到`管理设备证书`**

**打开`管理设备证书`找到`受限制的根证书颁发机构`**

****

![image-20230911183657301](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914942.png)

![image-20230911184352334](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914369.png)

**导入证书时，找不到证书就打开所有文件，导入即可。**

![image-20230911184243476](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914123.png)



**删除证书**

**打开chrome设置访问`隐私和安全`**

**在`安全`设置找到`管理设备证书`**

**打开`管理设备证书`找到`受限制的根证书颁发机构`**

**删除`PortSwigger CA`**

![image-20230911184451044](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914455.png)



### 2. Burpsuite 抓包微信小程序

#### 2.1 Clash + Burp suite

**使用chrome浏览器添加`Burpsuite`证书到`受信任的根证书颁发机构`中**

**配置 Clash for Windows**

安装`Service Mode`

![image-20230913154104842](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914444.png)

Windows安装会出现CMD命令提示

**导入配置文件**

​	配置文件信息

```yaml
port: 7890
socks-port: 7891
allow-lan: false
mode: Rule                 # 代理模式（Global, Rule, Direct）
log-level: info            # 日志记录级别（silent, info, warning, error, debug）
external-controller: :9090 # Clash外部控制器的地址和端口

proxies: 
  - name: burpsuite        # 代理名为 burpsuite
    type: http             # 代理类型
    server: 127.0.0.1      # Burp Suite地址
    port: 8080             # Burp Suite端口

proxy-groups:              # 代理组
  - name: ✨转发至         # 组名
    proxies:
      - burpsuite
    type: select

rules:
  # 过滤微信垃圾数据，防止干扰
  - DOMAIN,szminorshort.weixin.qq.com,DIRECT 
  - DOMAIN,long.open.weixin.qq.com,DIRECT
  - DOMAIN,res.wx.qq.com,DIRECT
  - DOMAIN-SUFFIX,qpic.cn,DIRECT
  - DOMAIN-SUFFIX,qlogo.cn,DIRECT
  - DOMAIN-SUFFIX,gtimg.cn,DIRECT
  - DOMAIN-SUFFIX,cdntips.net,DIRECT
  - DOMAIN-SUFFIX,weixinbridge.com,DIRECT
  - IP-CIDR,183.47.0.0/16,DIRECT
  ##
  # 过滤BP相关的进程-直连
  - PROCESS-NAME,java.exe,DIRECT
  - PROCESS-NAME,BurpSuitePro.exe,DIRECT
  ##
  # 把小程序主进程发起的请求转至BP
  - PROCESS-NAME,WeChatAppEx.exe,burpsuite
  - PROCESS-NAME,WechatBrowser.exe,burpsuite
  ##
  # 默认全部直连
  - MATCH,DIRECT

```

​	将配置文件写入一个`BP.yaml`文件，进行导入

![image-20230913165048142](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914473.png)

![image-20230913164816231](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914378.png)

**开启 TUN Mode**

![image-20230913165235616](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914482.png)

​	开启 TUN Mode后直接打开微信小程序即可抓包

![image-20230913165631288](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914324.png)



#### 2.2 Proxifier 抓包小程序

​	进入`proxy Server`添加`Burp Suite IP和端口`，同时`Protocol`设置为`HTTPS`

​	**Profile->Proxy Server**

![image-20231012062228229](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914629.png)

​	进入`Proxification Rules`

​	**Profile->Proxification Rules**

![image-20231012062613964](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914152.png)

​	添加需要转发代理的应用`WeChatAppEx.exe`，`WeChatAppEx.exe`是微信小程序的应用，可以在任务管理器上查看。

![image-20231012062812475](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914711.png)

![image-20231012062845153](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120914920.png)



​	抓包演示

![image-20231012063425283](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915688.png)



### 3. Burpsuite APP抓包

#### 3.1 模拟器必看

- 修改配置或者添加证书最好重启

- 添加代理模式下，无法从商店搜索、下载应用

- 9a5ba575.0证书无法在adb命令行和文件夹应用中删除，只能重命名

	

####  3.2 环境

​	**模拟器：安卓夜神模拟器**

​	**抓包工具：Burpsuite V2023.1.3**



#### 3.3 模拟器设置代理

​	`进入设置->点击WLAN->鼠标长时间点击WiredSSID->选择修改网络->选择代理手动`

​	填写宿主机IP和抓包端口

![image-20231011085305692](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915837.png)

​	返回Burpsuite 将原先的`127.0.0.1`修改为`模拟器填写的代理IP`

![image-20231011085605665](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915677.png)



#### 3.4 Https 抓包

##### 3.4.1 **安装证书**

​	准备好Burpsuite的证书包

​	将证书后缀修改为`*.crt`

​	拖动证书到模拟器，模拟器会自动跳转到文件目录

![image-20231011094525178](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915968.png)

​	将证书复制到`SD`目录下面或者使用文件助手直接传输到`Download`目录

![image-20231011095008156](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915642.png)

​	进入`设置->安全->从SD卡安装`，选择刚刚传输进来的证书

​	这时候会要求设置或输入`PIN`码，输入`PIN`后需要按一下回车

​	![image-20231011095311552](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915610.png)

​	安装成功后，可以在`用户凭据`查看

![image-20231011095349049](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915425.png)

​	可以简单的抓取APP HTTPS包

![image-20231011101254084](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915261.png)



**删除证书**

​	`设置->安全->用户凭证->选择证书删除`

​	删除证书后最好重启一下

![image-20231011090810729](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915507.png)

​	

##### 3.4.2 单向证书绕过

​	android7.0+版本都新增了证书验证，如果证书验证不通过就无法抓取数据包

![image-20231011102825523](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915731.png)

###### Xposed + JustTrustMe

​	在模拟器应用商店搜索`Xposed `即可安装

![image-20231011103243592](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915078.png)

​	打开`Xposed`安装`Version 89`，安装后重启模拟器。

![image-20231011103537118](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915951.png)

​	下载、安装[JustTrustMe](https://github.com/Fuzion24/JustTrustMe#justtrustme)

​	安装成功后，在`Xposed`模块勾选应用

![image-20231011103730000](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915982.png)

​	开启`Xposed`，进行抓包

![image-20231011104118330](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915247.png)





###### 手动安装证书

​	如果不想安装插件进行绕过证书验证，可以尝试下面方法。

​	`进入设置->点击WLAN->鼠标长时间点击WiredSSID->选择修改网络->选择代理手动`

​	填写宿主机IP和抓包端口

![image-20231011085305692](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915460.png)

​	返回Burpsuite 将原先的`127.0.0.1`修改为`模拟器填写的代理IP`

![image-20231011085605665](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915475.png)

​	打开浏览器设置端口代理跟burpsuite抓包的一样，然后访问`http://burpsuite`

![image-20230911181738635](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120915011.png)

![image-20230911181758283](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916651.png)

​	点击右边`CA Certificate`下载证书

​	现在将下载的`der`证书转换为`pem`证书

​	可以使用在线地址进行转换，也可以使用`openssl`进行转换

```bash
在线转为地址：http://web.chacuo.net/netsslcer2pem
openssl 转换命令：openssl x509 -inform der -in burp.der -out burp.pem
```

​	转换成功后，不会有任何显示，但是可以使用命令查看证书内容，证书内容中如果有`9a5ba575`就证明转换没有问题。

```bash
openssl x509 -subject_hash_old -in burp.pem
```

![image-20231012045751642](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916838.png)

​	现在将证书名称修改为`9a5ba575.0`，需要注意`0`就是后缀。

![image-20231012050811205](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916228.png)

​	打开模拟器开发者模式

​	**设置->平板电脑状态->点击5次版本号->返回点击开发者选项->开启**

![image-20231012050028414](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916747.png)

​	移动证书

​	移动证书到模拟器应用`Nox.exe`目录下，或者你的adb应用能调用的目录下。

![image-20231012050654899](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916056.png)

​	使用adb连接模拟器，这里我是将adb添加到了环境变量，如果没有添加到环境变量就需要去到模拟器应用当前目录下启动adb.exe。

```bash
adb connect 127.0.0.1:62001 //连接模拟器
adb root	//提升权限
adb remount	//重新挂载system分区

adb push 9a5ba575.0 /system/etc/security/cacerts/    //移动证书  
```

![image-20231012050854053](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916041.png)

​	抓包成功

​	![image-20231012051002160](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916593.png)

​	

**删除9a5ba575.0证书**

​	需要进入到`//system/etc/security/cacerts/`找到`9a5ba575.0`将其重命名，我尝试`adb`和手动删除都无法将其删除。

![image-20231012051724365](https://raw.githubusercontent.com/loulan-ling/CommonPacketCapture/main/img/202310120916261.png)

**参考链接**

​	https://www.dreamlove.top/3a777744

