## Linux与Windows的回车、换行

- Linux中每行后面只有<换行>`\n`

- windows中每行后面是<回车><换行>`\r\n`

导致的问题

- Linux文件在windows中打开，所有内容会变成一行
- Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号

CR(Carriage Return) LF(Line Feed)

- 回车CR-将光标移动到当前行的开头。
- 换行LF-将光标“垂直”移动到下一行。

Ubuntu会在每行自动插入<LF>

CR 用符号'\r'表示, 十进制ASCII代码是 13, 十六进制代码为 0x0D;
 LF 使用'\n'符号表示，ASCII代码是 10, 十六制为 0x0A。

所以 Windows 平台上换行在文本文件中是使用 0d 0a 两个字节表示，而 UNIX 和苹果平台上换行则是使用 0a 或 0d 一个字节表示。

