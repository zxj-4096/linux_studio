
Bash Shell 下打开一个TCP / UDP SOCKET

假设您想要在Linux服务器上打开TCP / UDP套接字，原因如下。例如检查特定的地址/端口是否可达、获取远程网页、调试一个restful API、连接到远程IRC服务器等。但是如果所在的Linux服务器是非常严格的，以致于没有任提供何标准工具，如netcat，curl或wget可能可用，这时候只能通过bash shell去完成上述的工作。

事实上，bash shell 的内置功能之一是通过/ dev / tcp（和/ dev / udp）设备文件（关于Linux下这些文件夹分别存在的意义，建议参考《Linux就该这么学》中对文件系统的相关描述）打开TCP / UDP套接字。在本教程的其余部分，我们来了解如何打开TCP / UDP套接字，并从bash shell中的套接字读取和写入。
1
1、在Bash Shell中打开或关闭TCP / UDP套接字

简而言之，您可以使用bash shell 中的以下语法打开TCP / UDP套接字。

$ exec {file-descriptor} </ dev / {protocol} / {host} / {port}

“文件描述符”是与每个套接字相关联的唯一的非负整数。文件描述符0,1和2分别保留给stdin，stdout和stderr。因此，您必须指定3或更高（以未使用者为准）作为文件描述符。

“<>”意味着套接字对于读写都是打开的。根据您的需要，您可以打开只读（<）或只写（>）的套接字。

“协议”字段可以是tcp或udp。“主机”和“端口”字段是不言自明的。

例如，要打开xmodulo.com的双向TCP套接字，使用HTTP端口和文件描述符3：

$ exec 3 <> / dev / tcp / xmodulo.com / 80

打开后，可以使用以下语法关闭读/写套接字。第一个命令关闭输入连接，后者关闭输出连接。

$ exec {file-descriptor} <＆ - $ exec {file-descriptor}>＆ -

2
2、在Bash Shell中读取或写入TCP / UDP套接字

打开套接字后，您可以向套接字写入消息或从套接字读取消息。

要将存储在$ MESSSAGE中的消息写入套接字：

$ echo -ne $ MESSAGE>＆3$ printf $ MESSAGE>＆3

要从套接字读取消息并将其存储在$ MESSAGE中：

$ read -r -u -n $ MESSAGE <＆3$ MESSAGE = $（dd bs = $ NUM_BYTES count = $ COUNT <＆3 2> / dev / null）

3
3、Bash Shell中的TCP / UDP套接字示例

这里我介绍几个打开和使用TCP套接字的shell脚本示例。

(1)获取远程网页并打印其内容

#!/bin/bash

exec 3<>/dev/tcp/xmodulo.com/80

echo -e "GET / HTTP/1.1\r\nhost: xmodulo.com\r\nConnection: close\r\n\r\n" >&3

cat <&3

(2)显示远程SSH服务器版本

#!/bin/bash

exec 3</dev/tcp/192.168.0.10/22

timeout 1 cat <&3

(3)从nist.gov显示当前时间

#!/bin/bash

cat </dev/tcp/time.nist.gov/13

(4)检查Internet连接

#!/bin/bash

 

HOST=www.mit.edu

PORT=80

 

(echo >/dev/tcp/${HOST}/${PORT}) &>/dev/null

if [ $? -eq 0 ]; then

    echo "Connection successful"

else

    echo "Connection unsuccessful"

fi

(5)对远程主机执行TCP端口扫描

#!/bin/bash

host=$1

port_first=1

port_last=65535

for ((port=$port_first; port<=$port_last; port++))

do

  (echo >/dev/tcp/$host/$port) >/dev/null 2>&1 && echo "$port open"

done

4
4、注意事项

在bash中打开一个socket 需要bash shell启用net-redirections（即使用“--enable-net-redirections”编译）。旧发行版可能禁用了bash的功能，在这种情况下，您将遇到以下错误：

/dev/tcp/xmodulo.com/80: No such file or directory

除了bash之外，已知可以在其他shell（如ksh或zsh）中使用套接字支持。