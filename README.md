## 工具介绍
Let’s Encrypt通配符证书申请，只能用DNS plugins的验证方式。其原理就是依照提示增加某个TXT记录的域名进行验证。整个流程都是需要配合certbot的提示并手动执行。  
如果想自动完成这个过程，根据官方文档提供的资料，需要写有两个hook脚本来替代人工操作：  
- **--manual-auth-hook**  
- **--manual-cleanup-hook**

## 使用步骤
### 一、下载代码
From https://github.com/broly8/letsencrypt-aliyun-dns-manual-hook.git 
```
git clone https://github.com/geekwolf/letsencrypt-aliyun-dns-ssl.git
```

### 二、配置appid和appsecret
申请配置具有AliyunDNSFullAccess权限(增删TXT记录需要)的用户密钥，并配置到config.ini文件

```
[aliyun]
appid=your-appid
appsecret=your-appsecret
```
### 三、配置日志
如果要启用日志记录功能.方便追查操作,可以修改log节点下的enable为True. logfile参数为日志位置和日志文件名.默认生成在/var/log下
```
[log]
enable=False
logfile=/var/log/dmlkdevtool.log
```
### 四、添加新域名证书

```
bash letsencrypt-create.sh  -d sit.simlinux.com -m geekwolf@simlinux.com
```
### 五、自动续期
```
certbot renew
续期配置：/etc/letsencrypt/renewal/[domain].conf
```
添加计划任务:
```
00 02 01 * * root /bin/certbot renew && /usr/local/tengine/sbin/nginx -s reload
```
如果想强制生成或者更新通配符证书，则使用 **-f** 参数。


```
sh  letsencrypt-create.sh  -d sit.simlinux.com -m geekwolf@simlinux.com -f
```

### 六、其他
1、可将阿里云appid和appsecret配置在manual-hook.py
```
def getAliyunDnsInstance():

    appid = <appid>
    appsecret = <appsecret>

    return aliyun.AliyunDns(appid, appsecret)

```
pyinstaller -F manual-hook.py 生产二进制文件，以规避appid和appsecret明文记录
