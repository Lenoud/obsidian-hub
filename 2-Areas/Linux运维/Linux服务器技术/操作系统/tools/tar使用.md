# tar使用

tar 命令打包与解压文件的使用方法。

```shell
#打包压缩为".tar.bz2"格式，注意压缩包文件名
tar -jcvf tmp.tar.bz2 /tmp/
#解压缩与解打包".tar.bz2"格式
tar -jxvf tmp.tar.bz2

#把/temp/目录直接打包压缩为".tar.gz"格式，通过"-z"来识别格式，"-cvf"和打包选项一致
tar -zcvf tmp.tar.gz /tmp/
#解压缩与解打包".tar.gz"格式
tar -zxvf tmp.tar.gz
```

分段压缩

```shell
tar -zcf - ROOT | split -b 10m - ROOT.tar.gz

（按10MB分割，也可以写1G等等自定义大小）
最后要提醒但是那两个"-"不要漏了，那是tar的ouput和split的input的参数。
把ROOT文件夹打包压缩成ROOT.tar.gz00，ROOT.tar.gz01，ROOT.tar.gz02……文件
```
