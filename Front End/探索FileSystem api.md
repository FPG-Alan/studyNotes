> 原文: https://www.html5rocks.com/en/tutorials/file/filesystem/

### 前言
一直以来, Web应用程序被限制读写文件系统, 这是其发展的一个阻碍. 但是随着Filesystem api的出现, web应用程序终于可以通过一个沙箱向用户的本地文件系统进行读写操作了.

这个API包含下面几个主要部分:

1. 读取和操作文件: File/Blob, FileList, FileReader
2. 创建和写入: Blob(), FileWriter
3. 文件夹和文件系统访问: DirectoryReader, FileEntry/DirectoryEntry, LocalFileSystem

### 浏览器支持和存储限制
在写这篇文章的时候, Google Chrome 是唯一实施了这个API的浏览器, 