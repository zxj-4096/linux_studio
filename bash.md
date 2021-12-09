bash的操作方法
1、光标移动篇

正确的做法是 #!/bin/sh 并不是所有系统上都有bash，也不是所有*nix系统都是linux。

sh是POSIX标准，bash是一种兼容的实现，并且如果没有--posix 开启POSIX mode的话它不是标准实现。

至于为什么很多严肃的系统上并不用bash : https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=bash
遵循最小原则，能用 sh 完成的就不必动用 bash 的扩展。虽然大部分 linux 系统 /bin/sh 都最终链接到 bash 了，但是写 /bin/sh 可以帮你禁掉大部分（但并非全部）bash 的扩展功能 。