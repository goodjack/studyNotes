| 命令    | 说明               |
| ------- | ------------------ |
| jobs    | 查看后台工作       |
| bg %num | 使后台变成running  |
| fg %num | 将后台工作放到前台 |
| kill    | 杀进程             |

| 压缩                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| tar -cvf jpg.tar *.jpg         | 将目录里所有 JPG 文件打包成 jpg.tar                          |
| tar -czf jpg.tar.gz *.jpg      | 将目录里所有 JPG 文件打包成 jpg.tar 后使用 gzip 压缩，最终为 jpg.tar.gz |
| tar -cjf jpg.tar.bz2 * jpg     | 将目录所有 JPG 文件打包成 jpg.tar 后使用 bzip2 压缩          |
| tar -cZf jpg.tar.Z *.jpg       | 将目录所有 jpg 文件打包成 jpg.tar 后使用 compress 压缩       |
| rar a jpg.rar *,.jpg           | rar 格式的压缩                                               |
| zip jpg.zip *.jpg              | zip 格式的压缩                                               |
| **解压**                       | **说明**                                                     |
| tar -xvf file.tar              | 解压 tar 包                                                  |
| tar -xzvf file.tar.gz          | 解压 tar.gz                                                  |
| tar -xjvf file.tar.bz2         | 解压 tar.bz2                                                 |
| tar -xZvf file.tar.Z           | 解压 tar.Z                                                   |
| unrar e file.rar               | 解压 rar                                                     |
| unzip file.zip                 | 解压 zip                                                     |
| tar -xzvf file.tar.gz -C /path | 解压到指定文件夹                                             |
|查看 Linux 内核版本|uname -a 或者 cat /proc/version|
|查看 Linux 系统版本的命令|lsb_release -a 或者 cat /etc/issue|
|查看 Linux 发行版本（推荐）|cat /etc/*release|





