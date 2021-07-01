openssh升级

> 系统版本：centos-7

1. 安装依赖包

    ```shell
    yum install wget gcc -y
    yum install zlib-devel openssl-devel  openssl -y
    yum install pam-devel libselinux-devel glibc make autoconf pcre-devel -y
    ```

    

2. 下载安装包

    ```shell
    wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.6p1.tar.gz
    wget https://www.openssl.org/source/old/1.1.1/openssl-1.1.1j.tar.gz
    wget http://prdownloads.sourceforge.net/libpng/zlib-1.2.11.tar.gz
    ```

    

3. 编译安装zlib

    ```shell
    tar xf zlib-1.2.11.tar.gz
    cd zlib-1.2.11
    ./configure --prefix=/usr/local/zlib
    make && make install
    ```

    

4. 编译安装openssl

    ```shell
    tar xf openssl-1.1.1j.tar.gz
    cd openssl-1.1.1j
    ./config --prefix=/usr/local/ssl -d shared
    make && make install
    echo '/usr/local/ssl/lib' >> /etc/ld.so.conf
    ldconfig -v
    
    #执行openssl version遇到下面为问题：
    openssl: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory
    执行两条命令即可：
    ln -s /usr/local/lib64/libssl.so.1.1 /usr/lib64/libssl.so.1.1
    ln -s /usr/local/lib64/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
    ```

    

5. 编译安装openssh

    ```shell
    tar xf openssh-8.6p1.tar.gz
    cd openssh-8.6p1
    ./configure --prefix=/usr/local/openssh --with-zlib=/usr/local/zlib --with-ssl-dir=/usr/local/ssl
    make && make install
    ```

    

6. 修改sshd_config文件

    ```shell
    echo 'PermitRootLogin yes' >>/usr/local/openssh/etc/sshd_config
    echo 'PubkeyAuthentication yes' >>/usr/local/openssh/etc/sshd_config
    echo 'PasswordAuthentication yes' >>/usr/local/openssh/etc/sshd_config
    ```

    

7. 备份原有文件，修改新文件指向

    ```shell
    mv /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
    cp /usr/local/openssh/etc/sshd_config /etc/ssh/sshd_config
    mv /usr/sbin/sshd /usr/sbin/sshd.bak
    cp /usr/local/openssh/sbin/sshd /usr/sbin/sshd
    mv /usr/bin/ssh /usr/bin/ssh.bak
    cp /usr/local/openssh/bin/ssh /usr/bin/ssh
    mv /usr/bin/ssh-keygen /usr/bin/ssh-keygen.bak
    cp /usr/local/openssh/bin/ssh-keygen /usr/bin/ssh-keygen
    mv /etc/ssh/ssh_host_ecdsa_key.pub /etc/ssh/ssh_host_ecdsa_key.pub.bak
    cp /usr/local/openssh/etc/ssh_host_ecdsa_key.pub /etc/ssh/ssh_host_ecdsa_key.pub
    ```

    

8. 复制配置文件

    ```shell
    cp -a contrib/redhat/sshd.init  /etc/init.d/sshd
    cp -a contrib/redhat/sshd.pam /etc/pam.d/sshd.pam
    chmod u+x /etc/init.d/sshd
    ```

    

9. 配置开机自启

    ```shell
    chkconfig --add sshd
    chkconfig sshd on
    systemctl enable sshd
    mv  /usr/lib/systemd/system/sshd.service{,.bak}
    ```

    

10. 重启

    ```shell
    service sshd status
    
    # 如果出现“Failed to get properties: Access denied”
    执行：systemctl daemon-reexec
    
    service sshd restart
    ```

    

11. 查看结果

    ```shell
    [root@ct1 openssh-8.6p1]# ssh -V
    OpenSSH_8.6p1, OpenSSL 1.1.1j  16 Feb 2021
    ```

    

    

