# mv

> 移动文件或目录，以及为文件或目录重命名

## 英文全拼

`move`

## 语法格式

```
mv [OPTIONS] SOURCE DEST
```

参数释义

| 字段      | 含义                                      |
| :-------- | :---------------------------------------- |
| [OPTIONS] | 可选命令参数，可省略，多个参数可合并简写  |
| SOURCE    | 源文件/源目录，支持单个、多个、通配符匹配 |
| DEST      | 目标路径/目标文件名                       |

## 参数

| 简写参数 | 完整长参数            | 参数作用                                                     |
| :------- | :-------------------- | :----------------------------------------------------------- |
| **-i**   | --interactive         | 覆盖文件前交互式弹窗提示，输入y确认覆盖，n取消，最常用安全参数 |
| **-f**   | --force               | 强制覆盖，不弹出任何提示，忽略文件权限，默认内置行为         |
| **-n**   | --no-clobber          | 禁止覆盖已有文件，存在同名文件直接跳过，不执行任何操作       |
| **-v**   | --verbose             | 可视化输出执行过程，打印每一步移动日志，排查问题专用         |
| **-u**   | --update              | 增量移动：仅源文件更新时间更新、或目标不存在时，才执行移动   |
| -b       | --backup              | 覆盖文件前自动备份原有目标文件，备份后缀默认~                |
| -S       | --suffix=SUFFIX       | 搭配-b使用，自定义备份文件后缀，替换默认波浪号               |
| -t       | --target-directory    | 指定目标目录，适配批量移动多文件，调换命令书写顺序           |
| -T       | --no-target-directory | 强制把目标当做文件，禁止识别为目录，规避目录匹配异常         |
| -Z       | --context             | 保留SELinux安全上下文，服务器运维专用                        |
| -h       | --help                | 输出mv命令帮助文档，查看参数说明                             |
| -V       | --version             | 查看mv命令版本信息                                           |

## 示例

重命名文件

```bash
# 将当前目录下的 file1.txt 重命名为 file2.txt
mv file1.txt file2.txt
```

重命名目录

```bash
# 将当前目录下的 dir1 目录重命名为 dir2
mv dir1 dir2
```

移动单个文件到指定目录

```bash
将 file.txt 移动到 /home/user/docs/ 目录下
mv file.txt /home/user/docs/
```

移动多个文件到指定目录

```bash
# 将 file1.txt、file2.txt 和 file3.txt 一次性移动到 /home/user/docs/ 目录下
mv file1.txt file2.txt file3.txt /home/user/docs/
```

```
# 使用通配符 * 移动所有 .txt 文件。
mv *.txt /home/user/docs/
```

移动目录到指定目录

```
# 将 project/ 目录移动到 /home/user/backup/ 目录下
mv project/ /home/user/backup/
```

交互式移动（防止误覆盖）

```bash
# 移动文件时，如果目标位置已有同名文件，会提示确认。
mv -i file.txt /home/user/docs/
```

强制覆盖移动

```bash
# 不进行任何询问，直接覆盖目标位置的同名文件
mv -f file.txt /home/user/docs/
```

显示详细操作过程

```bash
# 执行移动操作并显示详细的步骤信息。
mv -v *.txt /home/user/docs/
```

仅当源文件更新时移动

```bash
# 只有当 file.txt 比目标目录中的同名文件更新时，才执行移动操作
mv -uv file.txt /home/user/docs/
```

覆盖前创建备份

```bash
# 如果 file.txt 在目标目录已存在，在覆盖前会先为其创建一个备份（默认备份文件名为 file.txt~）。
mv -bv file.txt /home/user/docs/
```

