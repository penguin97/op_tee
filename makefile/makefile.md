# Makefile 文件编写语法规范详解

## 一、Makefile 概述

Makefile 是一种用于自动化构建和维护项目的脚本文件，主要用于编译和链接程序。它通过定义目标文件（targets）、依赖关系（dependencies）和生成目标的命令（recipes），让 `make` 工具能够自动推导出需要执行的操作，从而高效地构建项目。Makefile 的语法简洁而强大，但同时也需要遵循一定的规范，才能确保其正确性和可读性。

## 二、Makefile 的基本结构

### （一）目标（Targets）

目标文件是 Makefile 的核心，其定义格式为：

```makefile
target: dependencies
```

- **`target`**：目标文件的名称，可以是可执行文件、库文件或中间文件。

- **`dependencies`**：目标文件所依赖的文件列表。如果依赖文件发生变化，`make` 会重新构建目标文件。

### （二）命令（Recipes）

在目标定义的下一行，是用于生成目标文件的命令序列。命令必须以一个制表符（`\t`）开头，每条命令占一行。例如：

```makefile
target: dependencies
    <tab>command1
    <tab>command2
```

当目标文件需要重新构建时，`make` 会依次执行这些命令。

### （三）变量（Variables）

Makefile 允许定义变量，用于存储文件名、路径、编译器选项等信息。变量的定义格式为：

```makefile
VARIABLE = value
```

或者

```makefile
VARIABLE := value
```

使用变量时，可以通过 `$(VARIABLE)` 或 `${VARIABLE}` 的方式引用其值。

### （四）模式规则（Pattern Rules）

模式规则是一种特殊的规则，用于定义一组具有相同模式的目标文件的构建方式。例如：

```makefile
%.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@
```

这个规则表示，对于所有以 `.o` 结尾的目标文件，它们依赖于同名的 `.c` 文件，并且使用 `$(CC)` 编译器和 `$(CFLAGS)` 选项进行编译。

### （五）伪目标（Phony Targets）

伪目标是一种特殊的规则，它没有实际的文件作为目标，而是用于执行一些特定的操作。例如：

```makefile
.PHONY: clean
clean:
    rm -f *.o
```

这个伪目标用于清理项目中的中间文件。当运行 `make clean` 时，`make` 会执行 `rm -f *.o` 命令，删除所有的 `.o` 文件。

## 三、Makefile 的语法规范

### （一）目标和依赖

1. **目标文件名**
   
   - 目标文件名应该清晰地反映其内容和用途。例如，可执行文件可以命名为 `program`，中间文件可以命名为 `module.o`。

2. **依赖关系**
   
   - 依赖关系应该准确地描述目标文件所依赖的文件。如果依赖文件的顺序不正确，可能会导致构建失败或生成错误的目标文件。例如：
     
     ```makefile
     program: module1.o module2.o
     ```

3. **多目标规则**
   
   - 如果多个目标文件具有相同的依赖关系和构建命令，可以将它们放在同一个规则中。例如：
     
     ```makefile
     module1.o module2.o: module.h
     ```

### （二）命令

1. **制表符**
   
   - ****命令必须以制表符开头，不能使用空格代替****。这是因为 `make` 会将制表符作为命令的标识符，如果使用空格，`make` 会报错。

2. **命令序列**
   
   - 如果需要执行多个命令，可以将它们放在同一个规则中，每条命令占一行。例如：
     
     ```makefile
     target: dependencies
        command1
        command2
     ```

3. **命令分隔符**
   
   - 如果需要在一行中执行多个命令，可以使用分号（`;`）作为分隔符。例如：
     
     ```makefile
     target: dependencies
        command1; command2
     ```
     
     但这种写法不太推荐，因为它会降低命令的可读性。

4. **命令注释**
   
   - 可以在命令后面添加注释，用于说明命令的作用。例如：
     
     ```makefile
     target: dependencies
        command1 # This command does something
     ```

### （三）变量

1. **变量命名**
   
   - 变量名应该具有描述性，能够清晰地反映其含义。例如，`CC` 表示 C 编译器，`CFLAGS` 表示 C 编译器的选项。

2. **变量赋值**
   
   变量的赋值可以使用 `=` ，`:=` ，`+=`。
   
   - `=` 表示递归扩展赋值，`make` 会在使用变量时展开其值。
   
   - `:=` 表示简单赋值，`make` 会在定义变量时立即展开其值。 
   
   - `+=` 是一种特殊的赋值操作符，用于向变量追加值。它会将右侧的值追加到变量的现有值之后。是一种特殊的赋值操作符，用于向变量追加值。它会将右侧的值追加到变量的现有值之后。
   
   ```makefile
   CC = gcc
   CFLAGS = -Wall -O2
   ```
   或者
   ```makefile
   CC := gcc
   CFLAGS := -Wall -O2
   ```
   
3.**变量引用**
   
   - 引用变量时，可以使用 `$(VARIABLE)` 或 `${VARIABLE}` 的方式。这两种方式是等价的，但 `${VARIABLE}` 在某些情况下可以提高可读性。例如：
     
   ```makefile
   target: dependencies
      $(CC) $(CFLAGS) -o $@ $<
   ``` 
   或者 
   ```makefile
   target: dependencies
      ${CC} ${CFLAGS} -o $@ $<
   ```

### （四）模式规则

1. **模式匹配**
   
   - 模式规则使用 `%` 作为通配符，表示任意长度的字符序列。例如：
     
     ```makefile
     %.o: %.c
     ```
     
     这表示所有以 `.o` 结尾的目标文件都依赖于同名的 `.c` 文件。

2. **自动变量**
   
   - 在模式规则中，可以使用自动变量来引用目标文件和依赖文件。常用的自动变量有：
     
     - `$@`：表示目标文件的名称。
     
     - `$<`：表示依赖文件列表中的第一个文件。
     
     - `$^`：表示依赖文件列表中的所有文件。例如：
       
       ```makefile
       %.o: %.c
          $(CC) $(CFLAGS) -c $< -o $@
       ```

### （五）伪目标

1. **伪目标定义**
   
   - 伪目标的定义格式为：
     
     ```makefile
     .PHONY: target
     ```
     
     这表示 `target` 是一个伪目标，它没有实际的文件作为目标。

2. **伪目标用途**
   
   - 伪目标通常用于执行一些特定的操作，例如清理项目、安装程序等。例如：
     
     ```makefile
     .PHONY: clean install
     clean:
        rm -f *.o
     install:
        cp program /usr/bin
     ```

### （六）条件语句

1. **条件判断**
   
   - Makefile 支持条件语句，用于根据某些条件执行不同的规则。条件语句的格式为：
     
     ```makefile
     ifeq (arg1, arg2)
        command1
     else
        command2
     endif
     ```
     
     或者
     
     ```makefile
     ifneq (arg1, arg2)
        command1
     else
        command2
     endif
     ```
     
     例如：
     
     ```makefile
     ifeq ($(CC), gcc)
     CFLAGS += -std=c99
     else
     CFLAGS += -Wall
     endif
     ```

2. **条件变量**
   
   - 条件语句中可以使用变量进行判断。例如：
     
     ```makefile
     ifeq ($(DEBUG), yes)
     CFLAGS += -g
     endif
     ```

## 四、Makefile 示例

以下是一个完整的 Makefile 示例，展示了如何结合上述规范编写一个用于构建 C 程序的 Makefile 文件：

```makefile
# 定义变量
CC = gcc
CFLAGS = -Wall -O2
LDFLAGS =
SOURCES = main.c module1.c module2.c
OBJECTS = $(SOURCES:.c=.o)
EXECUTABLE = program

# 默认目标
all: $(EXECUTABLE)

# 构建可执行文件
$(EXECUTABLE): $(OBJECTS)
    $(CC) $(LDFLAGS) -o $@ $^

# 构建对象文件
%.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@

# 清理目标
.PHONY: clean
clean:
    rm -f $(OBJECTS) $(EXECUTABLE)

# 安装目标
.PHONY: install
install:
    cp $(EXECUTABLE) /usr/bin
```

## 五、总结

Makefile 是一个强大的工具。

[^1][makefile 菜鸟教程](https://www.cainiaoya.com/makefile/makefile-directives.html)  
[^2][makefile 教程 廖雪峰](https://liaoxuefeng.com/books/makefile/install-make/index.html)
