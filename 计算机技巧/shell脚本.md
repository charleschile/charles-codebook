# Shell脚本

tpch脚本参考

```shell
#!/bin/bash
# 指定使用 Bash 作为脚本的解释器

write_to_file()
{
    # 定义一个名为 write_to_file 的函数，用于生成 SQL 文件

    file="loaddata.sql"
    # 定义一个变量 file，设置其值为 "loaddata.sql"，表示要生成的 SQL 文件的名称

    if [ ! -f "$file" ] ; then
        # 如果文件 "loaddata.sql" 不存在（使用 -f 检查文件是否存在）
        touch "$file"
        # 使用 touch 命令创建一个空的 "loaddata.sql" 文件
    fi
    
    echo 'USE tpch;' >> $file
    # 将 "USE tpch;" 写入 "loaddata.sql" 文件，切换到 tpch 数据库

    DIR=`pwd`
    # 使用 pwd 命令获取当前目录路径，并将结果赋值给变量 DIR

    for tbl in `ls *.tbl`; do
        # 使用 for 循环遍历当前目录中所有以 .tbl 结尾的文件

        table=$(echo "${tbl%.*}" | tr '[:lower:]' '[:upper:]')
        # 去除文件扩展名（.tbl），并将文件名转换为大写，以便与表名匹配

        echo "LOAD DATA LOCAL INFILE '$DIR/$tbl' INTO TABLE $table" >> $file
        # 将 "LOAD DATA LOCAL INFILE ..." 命令写入 "loaddata.sql" 文件，用于从 .tbl 文件导入数据到对应的表

        echo "FIELDS TERMINATED BY '|' LINES TERMINATED BY '|\n';" >> $file
        # 将 "FIELDS TERMINATED BY '|' LINES TERMINATED BY '|\n';" 写入 "loaddata.sql"，用于定义字段和行的分隔方式
    done
 }
# 结束函数的定义

write_to_file
# 调用 write_to_file 函数，生成 "loaddata.sql" 文件


```





Shell 脚本是一个包含一系列命令的文本文件，用于自动化任务或执行一系列的操作。Shell 脚本通常有特定的格式和结构，以下是 Shell 脚本的一般格式。

### Shell 脚本的基本格式

1. **脚本文件的开头：Shebang（#!）**

   - 在脚本的第一行，需要使用 `#!` 来指定要使用的 Shell 程序，比如 `/bin/bash` 或 `/bin/sh`。

   - 例如，Bash 脚本的第一行通常是：

     ```shell
     #!/bin/bash
     ```

2. **注释（#）**

   - 使用 `#` 表示注释，注释内容不会被执行，用于解释脚本的功能或添加备注信息。

   - 例如：

     ```shell
     # 这是一个简单的 Shell 脚本
     ```

3. **变量定义**

   - Shell 脚本中可以定义变量，用于存储数据或参数。

   - 例如：

     ```shell
     name="Alice"
     echo "Hello, $name"
     ```

4. **命令和语句**

   - 脚本主体部分包含要执行的命令和语句。

   - 例如：

     ```shell
     echo "Welcome to Shell scripting!"
     ```

5. **控制结构**

   - Shell 脚本支持常见的控制结构，如 `if-else`、`for` 循环、`while` 循环等。

   - 例如：

     ```shell
     if [ "$name" = "Alice" ]; then
       echo "Hello, Alice!"
     else
       echo "Who are you?"
     fi
     ```

6. **函数定义**

   - Shell 脚本可以定义函数，用于封装一段逻辑以便复用。

   - 例如：

     ```shell
     greet() {
       echo "Hello, $1!"
     }
     
     greet "Bob"
     ```

### Shell 脚本的完整示例

```shell
#!/bin/bash

# 脚本示例：简单的备份脚本

# 变量定义
source_dir="/home/user/documents"
backup_dir="/home/user/backup"

# 创建备份目录（如果不存在）
mkdir -p "$backup_dir"

# 备份文件
cp -r "$source_dir"/* "$backup_dir"

# 显示备份结果
echo "Backup completed from $source_dir to $backup_dir."
```

### 编写和运行 Shell 脚本的步骤

1. **创建脚本文件**：使用文本编辑器创建一个文件，并将其保存为 `.sh` 文件，例如 `backup.sh`。

2. 添加执行权限

   ：使用 

   ```
   chmod +x
   ```

    命令为脚本添加执行权限，例如：

   ```shell
   chmod +x backup.sh
   ```

3. 运行脚本

   ：通过以下方式运行脚本：

   ```shell
   ./backup.sh
   ```

这种格式和结构使得 Shell 脚本易于理解和维护。









### . **`chmod +x` 的作用**

- `chmod +x` 用于为文件添加**可执行权限**。

- `+x` 表示将执行权限（executable）添加到文件或目录上。

- 例如：

  ```
  bash
  
  
  复制代码
  chmod +x script.sh
  ```

  执行这条命令后，

  ```
  script.sh
  ```

   文件就具备了可执行权限，可以直接运行：

  ```
  bash
  
  
  复制代码
  ./script.sh
  ```

### 2. **`chmod` 的常见用法**

`chmod` 的基本语法是：

```
bash


复制代码
chmod [选项] 模式 文件名
```

- **模式（Mode）**可以通过符号模式（Symbolic mode）或八进制模式（Octal mode）指定。

#### 2.1 **符号模式（Symbolic mode）**

符号模式用 `u`、`g`、`o` 和 `a` 指定用户类型，用 `+`、`-` 和 `=` 修改权限。

- `u`：文件的拥有者（user）
- `g`：文件的所属组（group）
- `o`：其他用户（others）
- `a`：所有用户（all）

权限类型：

- `r`：读取权限（read）
- `w`：写入权限（write）
- `x`：执行权限（execute）

示例：

- 添加权限

  ```
  bash
  
  
  复制代码
  chmod u+r file.txt  # 为文件拥有者添加读取权限
  chmod g+w file.txt  # 为文件所属组添加写入权限
  chmod o+x file.txt  # 为其他用户添加执行权限
  ```

- 移除权限

  ```
  bash
  
  
  复制代码
  chmod u-r file.txt  # 移除文件拥有者的读取权限
  chmod g-w file.txt  # 移除文件所属组的写入权限
  chmod o-x file.txt  # 移除其他用户的执行权限
  ```

- 设置特定权限

  ```
  bash
  
  
  复制代码
  chmod u=rw file.txt  # 仅为文件拥有者设置读取和写入权限
  chmod a=r file.txt   # 为所有用户设置只读权限
  ```

#### 2.2 **八进制模式（Octal mode）**

八进制模式使用数字来表示权限，其中：

- `4` 表示读取权限（`r`）
- `2` 表示写入权限（`w`）
- `1` 表示执行权限（`x`）

权限值通过将这些数字相加来组合：

- `7` = 4 + 2 + 1（读取、写入和执行权限）
- `6` = 4 + 2（读取和写入权限）
- `5` = 4 + 1（读取和执行权限）
- `4` = 4（只读权限）

示例：

- 设置文件权限为 755

  （用户有读、写、执行权限，组和其他用户只有读、执行权限）：

  ```
  bash
  
  
  复制代码
  chmod 755 script.sh
  ```

- 设置文件权限为 644

  （用户有读、写权限，组和其他用户只有读权限）：

  ```
  bash
  
  
  复制代码
  chmod 644 file.txt
  ```

### 3. **`chmod` 的选项**

- **`-R`（递归）**：对目录及其子目录中的所有文件应用权限更改。

  ```
  bash
  
  
  复制代码
  chmod -R 755 /path/to/directory
  ```

  这会将 `/path/to/directory` 及其子目录中的所有文件和子目录的权限设置为 755。

- **`-v`（详细模式）**：显示权限更改的详细信息。

  ```
  bash
  
  
  复制代码
  chmod -v 644 file.txt
  ```

- **`--reference=<file>`**：将目标文件的权限设置为参考文件的权限。

  ```
  bash
  
  
  复制代码
  chmod --reference=ref_file.txt file.txt
  ```

### 总结

- **`chmod +x`**：为文件添加可执行权限。
- **`chmod`**：可以灵活设置文件或目录的各种权限，包括读、写、执行权限的添加、删除和设置。