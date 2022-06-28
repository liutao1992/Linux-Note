### 配置静态IP[CentOS 7]

1. 打开文件
    ```
    /etc/sysconfig/network-scripts/ifcfg-ens33
    ```
2. 修改内容如下，其他字段保持不变

    ```
    ONBOOT="yes"         # 检查该字段，是否是开机启动
    BOOTPROTO="none"     # 该字段可为none或者是static

    # 新增内容
    IPADDR=192.168.42.150
    NETMASK=255.255.255.0
    GATEWAY=192.168.42.2
    DNS=114.114.114.114
    ```

3. 检查DNS服务器地址设置

    打开文件`vim /etc/resolv.conf`, 若该文件内容为空，则新增内容如下:
    ```
    nameserver 114.114.114.114
    ```

4. 重启服务
    ```
    systemctl restart network
    ```

5. 检查网络是否正常连接

    ```
    ping -c 3 www.baidu.com
    ```
