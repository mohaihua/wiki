将目录下的所有文件每行一个列出来

```sh
shell> find ./  -printf "%f\n"
```

- [How to output only file names (with spaces) in ls -Al?](https://unix.stackexchange.com/questions/70614/how-to-output-only-file-names-with-spaces-in-ls-al)

快速生成大文件

```sh
sell> dd if=/dev/zero of=/tmp/my_file bs=1M count=1000
```

快速生成每次都不一样的大文件

```sh
shell> head -c 10M < /dev/urandom > /tmp/my_file
```

小写转换成大写

```sh
shell> sha1sum test | tr a-z A-Z
```

大写转换成小写

```sh
shell> echo 'A5F5E9413C558753025495DAF6A0A7B47E55402B' | tr A-Z a-z
```

删除文件名带 `-` 的文件

```sh
shell> rm ./-A_NNMHDB3MW3eB
```

tree 查看所有文件及权限

```sh
shell> tree -aup
```

- [View permission / owner of full directory tree](https://unix.stackexchange.com/questions/82361/view-permission-owner-of-full-directory-tree)

复制所有文件（包括隐藏文件）

```sh
shell> cp -R /path/to/src/. /path/to/dest/
```

- [Linux下cp目录下的带隐藏文件的子目录](https://blog.csdn.net/yockie/article/details/52872623)

删除超过特定大小的文件

```sh
find . -size +100k -delete
```

- [How to get all the files exceeding certain size and deleting them](https://stackoverflow.com/questions/5057041/how-to-get-all-the-files-exceeding-certain-size-and-deleting-them)

## dpkg

查看安装的所有软件

```sh
shell> dpkg -l
shell> dpkg -l | grep ftp
```

## du

计算一个目录的大小

```sh
du -sh folder
```

只显示一级目录的大小

```sh
du -sh --max-depth=0
```

- [Linux中查看各文件夹大小命令du -h --max-depth=1](https://blog.csdn.net/ouyang_peng/article/details/10414499)

## 进程

后台执行进程

```sh
shell> nohup /usr/bin/php demo.php > /tmp/demo.log 2>&1 &
```

批量杀死进程

```sh
shell> ps -ef | grep demo.php | grep -v grep | awk '{print $2}' | xargs kill -9
```

查看特定端口所属进程

```sh
shell> netstat -anp | grep :4730
```

查找正在运行的进程

```sh
shell> ps -ef | grep php
shell> ps -aux | grep php
```

## grep

查找包含任意一个关键字的行

```sh
shell> grep -E 'word1|word2|word3' file.txt
```

查找包含所有关键字的行

```sh
shell> grep word1 file.txt | grep word2 |grep word3
```

- [grep 同时满足多个关键字和满足任意关键字](https://www.cnblogs.com/smallrookie/p/6102691.html)

统计 test2 中有，test1 中没有的行

```sh
shell> grep -vFf test1.csv test2.csv
```

统计两个文本文件的相同行

```sh
shell> grep -Ff test1.csv test2.csv
```

根据前面两列的值是否相同去重

```sh
sort -u -t, -k1,2 test.txt
```

- [Is there a way to 'uniq' by column?](https://stackoverflow.com/questions/1915636/is-there-a-way-to-uniq-by-column)

计算指定列的总和

```sh
awk -F',' '{sum += $4};END {print sum}' test.txt
```

## 快捷键

```sh
ctrl+a: 光标移到行首
ctrl+e: 光标移到行尾
ctrl+l: 清屏，相当于clear
ctrl+u: 清除光标前至行首间的所有内容
ctrl+shift+t: 打开 Tab 新标签
```
