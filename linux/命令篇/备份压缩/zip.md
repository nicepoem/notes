# zip

> `zip` 命令是一个用于**文件压缩和打包**的常用工具。它不仅能将多个文件合并为一个压缩文件以节省磁盘空间，还方便文件传输和备份。

## 英文全拼

```
zip = zip archive
```

## 语法

```bash
zip [参数] [压缩包名称.zip] [要压缩的文件或目录列表]
```
## 参数



## 实战示例

**示例 1：压缩单个文件**

```bash
# 将 file1.txt 文件压缩为 archive.zip
zip archive.zip file1.txt
```

**示例 2：压缩多个文件**

```bash
# 将 file1.txt file2.jpg file3.png 文件压缩为 archive.zip
zip archive.zip file1.txt file2.jpg file3.png
```

**示例 3：递归压缩整个目录（最常用）**

```bash
# 将 /home/user/Documents/MyProject 压缩为 myfolder.zip
zip -r myfolder.zip /home/user/Documents/MyProject
```

*注意：如果不加 `-r`，只会压缩空文件夹，不会包含里面的内容。*

**示例 5：更新已有的压缩包**

```bash
zip -u archive.zip newfile.txt
```

*如果 `archive.zip` 已有 `newfile.txt` 且比压缩包里的新，则替换；否则添加进去。*



unzip

解压缩