# GCC 编译器使用指南

> 基于历史文档更新，适用于 GCC 现代版本 (GCC 7+)

## 简介

GCC (GNU Compiler Collection) 是 GNU 项目下的多语言编译器套件，支持 C、C++、Objective-C、Fortran、Ada、Go 等多种编程语言。本指南重点介绍 C/C++ 编译的相关用法。

### 编译过程（4个阶段）

1. **预处理 (Preprocessing)** - 生成 `.i` 文件
   ```bash
   gcc -E source.c -o source.i
   ```

2. **编译 (Compilation)** - 生成 `.s` 汇编文件
   ```bash
   gcc -S source.i -o source.s
   ```

3. **汇编 (Assembly)** - 生成 `.o` 目标文件
   ```bash
   gcc -c source.s -o source.o
   ```

4. **链接 (Linking)** - 生成可执行文件
   ```bash
   gcc source.o -o program
   ```

### 快速入门

```bash
# 编译单个 C 文件
gcc hello.c -o hello

# 编译多个 C 文件
gcc main.c utils.c -o program

# 编译 C++ 文件 (推荐使用 g++)
g++ hello.cpp -o hello

# 分步编译
gcc -c file.c          # 生成 file.o
gcc file.o -o program  # 链接成可执行文件
```

## 常用编译选项

### 基础选项

| 选项 | 说明 | 示例 |
|------|------|------|
| `-o <file>` | 指定输出文件名 | `gcc -o program source.c` |
| `-c` | 只编译不链接，生成目标文件 | `gcc -c source.c` |
| `-S` | 生成汇编代码 | `gcc -S source.c` |
| `-E` | 只进行预处理 | `gcc -E source.c` |
| `-v` | 显示详细的编译过程 | `gcc -v source.c` |
| `-###` | 显示但不执行编译命令 | `gcc -### source.c` |

### 优化级别

| 优化级别 | 说明 | 建议场景 |
|----------|------|----------|
| `-O0` | 不优化（默认） | 调试阶段 |
| `-O1` | 基本优化 | 一般开发 |
| `-O2` | 推荐优化级别 | 发布版本 |
| `-O3` | 激进优化 | 性能关键应用 |
| `-Os` | 优化代码大小 | 嵌入式系统 |
| `-Ofast` | 忽略严格标准，追求速度 | 科学计算 |
| `-Og` | 优化但不影响调试 | 开发调试 |

### 调试信息

| 选项 | 说明 | 生成格式 |
|------|------|----------|
| `-g` | 生成基础调试信息 | 默认格式 |
| `-ggdb` | 生成 GDB 专用调试信息 | GDB 优化格式 |
| `-g3` | 包含宏定义等额外信息 | 最详细信息 |
| `-gz` | 压缩调试信息 | DWARF-5 |

```bash
# 调试编译示例
gcc -g -O0 program.c -o program_debug

# 发布编译示例
gcc -O2 -DNDEBUG program.c -o program_release
```

### 警告控制

| 选项 | 说明 |
|------|------|
| `-Wall` | 启用大部分常见警告 |
| `-Wextra` | 启用额外警告 |
| `-Wpedantic` | 严格遵循 ISO C/C++ 标准 |
| `-Werror` | 将警告视为错误 |
| `-Wno-<warning>` | 禁用特定警告 |
| `-Wshadow` | 警告变量遮蔽 |
| `-Wunused` | 警告未使用的变量/函数 |

```bash
# 严格的编译检查
gcc -Wall -Wextra -Werror -Wpedantic source.c -o program

# 生产环境推荐
gcc -Wall -Wextra -O2 source.c -o program
```

### 头文件和库路径

| 选项 | 说明 | 示例 |
|------|------|------|
| `-I<dir>` | 添加头文件搜索路径 | `-I/usr/local/include` |
| `-L<dir>` | 添加库文件搜索路径 | `-L/usr/local/lib` |
| `-l<name>` | 链接指定的库 | `-lpthread -lm` |
| `-include <file>` | 强制包含头文件 | `-include config.h` |
| `-isystem <dir>` | 添加系统头文件路径 | `-isystem /opt/include` |

```bash
# 包含第三方库
gcc program.c -I./include -L./lib -lmylib -o program

# 使用 pkg-config（推荐）
gcc program.c $(pkg-config --cflags --libs gtk+-3.0) -o program
```

### 语言标准

#### C 语言标准
- `-std=c89` / `-std=c90` - ANSI C / ISO C90
- `-std=gnu89` - GNU C90（默认）
- `-std=c99` - ISO C99
- `-std=gnu99` - GNU C99
- `-std=c11` - ISO C11
- `-std=gnu11` - GNU C11（GCC 5+ 默认）
- `-std=c17` / `-std=c18` - ISO C17/C18
- `-std=gnu17` - GNU C17
- `-std=c2x` - 即将到来的 C2x 标准

#### C++ 语言标准
- `-std=c++98` / `-std=c++03` - C++98/C++03
- `-std=gnu++98` - GNU C++98
- `-std=c++11` - C++11
- `-std=gnu++11` - GNU C++11
- `-std=c++14` - C++14
- `-std=gnu++14` - GNU C++14
- `-std=c++17` - C++17
- `-std=gnu++17` - GNU C++17（GCC 11+ 默认）
- `-std=c++20` - C++20
- `-std=c++23` - C++23（实验性）

```bash
# 使用 C11 标准编译
gcc -std=c11 -pedantic program.c -o program

# 使用 C++17 标准编译
g++ -std=c++17 -O2 program.cpp -o program
```

### 处理器架构优化

| 选项 | 说明 |
|------|------|
| `-march=native` | 针对当前 CPU 优化 |
| `-mtune=native` | 针对当前 CPU 调优 |
| `-m64` | 生成 64 位代码（默认） |
| `-m32` | 生成 32 位代码 |
| `-msse4.2` | 启用 SSE4.2 指令集 |
| `-mavx2` | 启用 AVX2 指令集 |

```bash
# 针对当前 CPU 最大化优化
gcc -march=native -O3 program.c -o program_optimized

# 32位兼容编译
gcc -m32 program.c -o program32
```

### 安全性选项

| 选项 | 说明 | 安全特性 |
|------|------|----------|
| `-fstack-protector` | 栈溢出保护 | 中等保护 |
| `-fstack-protector-strong` | 强栈保护 | 推荐 |
| `-fstack-protector-all` | 全栈保护 | 最强保护 |
| `-D_FORTIFY_SOURCE=2` | 缓冲区溢出检查 | 运行时检查 |
| `-Wformat-security` | 格式化字符串安全警告 | 编译时检查 |
| `-fPIE -pie` | 位置无关可执行文件 | ASLR 支持 |
| `-fPIC` | 位置无关代码 | 共享库必需 |

```bash
# 安全编译示例
gcc -O2 -fstack-protector-strong -D_FORTIFY_SOURCE=2 \
    -Wformat -Wformat-security program.c -o program_secure

# 创建安全共享库
gcc -fPIC -shared -Wl,-z,now,-z,relro library.c -o libexample.so
```

### 链接器选项

通过 `-Wl,` 传递选项给链接器：

```bash
# 设置堆栈大小
gcc -Wl,--stack,8388608 program.c -o program  # 8MB 堆栈

# 安全链接选项
gcc -Wl,-z,now,-z,relro,-z,defs program.c -o program

# 设置入口点
gcc -Wl,-e,my_entry_point program.c -o program

# 移除未使用符号
gcc -Wl,--gc-sections program.c -o program
```

### 诊断和分析

| 选项 | 说明 |
|------|------|
| `-save-temps` | 保存临时文件 (.i, .s, .o) |
| `-fdump-tree-all` | 输出所有中间表示树 |
| `-ftime-report` | 报告各阶段耗时 |
| `-fstack-usage` | 生成堆栈使用报告 |
| `-fprofile-arcs` | 生成代码覆盖率数据 |
| `-ftest-coverage` | 生成 .gcno 覆盖率文件 |

```bash
# 分析编译过程
gcc -ftime-report -O2 program.c -o program

# 生成覆盖率数据
gcc -fprofile-arcs -ftest-coverage program.c -o program
```

## 现代功能特性

### 地址消毒器 (AddressSanitizer)
```bash
# 检测内存错误
gcc -fsanitize=address -g program.c -o program_asan

# 检测未定义行为
gcc -fsanitize=undefined -g program.c -o program_ubsan

# 检测内存泄漏
gcc -fsanitize=leak -g program.c -o program_lsan

# 线程消毒器
gcc -fsanitize=thread -g program.c -o program_tsan
```

### 链接时优化 (LTO)
```bash
# 启用 LTO
gcc -flto -O2 file1.c file2.c -o program

# 使用并行 LTO
gcc -flto=auto -O2 file1.c file2.c -o program
```

### 控制流保护
```bash
# 控制流完整性保护
gcc -fcf-protection=full program.c -o program
```

## 实用示例

### 1. 标准开发编译
```bash
gcc -Wall -Wextra -Wpedantic -std=c11 -g -O0 program.c -o program_debug
```

### 2. 生产环境发布
```bash
gcc -Wall -Wextra -std=c11 -O2 -DNDEBUG \
    -fstack-protector-strong -D_FORTIFY_SOURCE=2 \
    program.c -o program_release
```

### 3. 创建共享库
```bash
# 编译为位置无关代码
gcc -fPIC -shared -Wall -O2 library.c -o libmylib.so

# 使用版本号
gcc -fPIC -shared -Wall -O2 library.c -Wl,-soname,libmylib.so.1 \
    -o libmylib.so.1.0.0
ln -sf libmylib.so.1.0.0 libmylib.so
ln -sf libmylib.so.1.0.0 libmylib.so.1
```

### 4. 交叉编译
```bash
# ARM 架构交叉编译
arm-linux-gnueabihf-gcc -march=armv7-a -mfpu=neon-vfpv4 \
    -mfloat-abi=hard program.c -o program_arm

# RISC-V 架构交叉编译
riscv64-linux-gnu-gcc -march=rv64gc -mabi=lp64d \
    program.c -o program_riscv
```

### 5. 使用 Makefile 管理
```makefile
CC = gcc
CFLAGS = -Wall -Wextra -O2 -std=c11
LDFLAGS = -lm

TARGET = program
SRCS = main.c utils.c
OBJS = $(SRCS:.c=.o)

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(OBJS) -o $@ $(LDFLAGS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)

.PHONY: all clean
```

## 故障排除

### 常见错误及解决方案

1. **未定义的引用错误**
   ```bash
   # 错误: undefined reference to `function_name'
   # 解决方案: 确保链接了正确的库
   gcc program.c -lm -lpthread -o program
   ```

2. **头文件找不到**
   ```bash
   # 错误: fatal error: header.h: No such file or directory
   # 解决方案: 使用 -I 指定路径
   gcc -I/usr/local/include -I./include program.c -o program
   ```

3. **库文件找不到**
   ```bash
   # 错误: cannot find -llibrary
   # 解决方案: 使用 -L 指定库路径
   gcc -L/usr/local/lib program.c -lmylib -o program
   ```

4. **架构不兼容**
   ```bash
   # 错误: wrong ELF class: ELFCLASS64
   # 解决方案: 统一使用 -m32 或 -m64
   gcc -m32 program.c -o program32
   ```

### 调试技巧

```bash
# 查看预处理器宏定义
gcc -dM -E - < /dev/null

# 查看搜索路径
gcc -print-search-dirs

# 查看支持的目标架构
gcc -print-multi-lib

# 查看编译器版本和配置
gcc -v
```

## 性能优化建议

1. **合理使用优化级别**：开发时使用 `-O0 -g`，测试时使用 `-O2`，发布时使用 `-O3 -march=native`
2. **启用链接时优化**：对于大型项目使用 `-flto`
3. **使用 PGO**：对于性能关键应用使用配置文件引导优化
4. **选择合适的架构**：使用 `-march=native` 针对当前 CPU 优化
5. **减少调试信息**：发布版本使用 `-g1` 或省略 `-g`

## 兼容性说明

- GCC 4.x 系列：已过时，不建议使用
- GCC 5.x-7.x：基本功能完善，适合大多数场景
- GCC 8.x+：支持 C17/C++17 标准，推荐使用
- GCC 11.x+：支持 C++20 标准，现代特性完整

## 资源链接

1. [GCC 官方文档](https://gcc.gnu.org/onlinedocs/)
2. [GCC 编译选项手册](https://gcc.gnu.org/onlinedocs/gcc/Option-Summary.html)
3. [C 语言标准文档](https://www.open-std.org/jtc1/sc22/wg14/)
4. [C++ 标准文档](https://isocpp.org/std/the-standard)

## 版本历史

- **2024-01-15** - 更新现代 GCC 选项，添加安全特性、消毒器、LTO 等
- **2024-01-10** - 重新组织内容结构，添加实用示例
- **2024-01-05** - 更新语言标准，添加 C17/C++20 支持
- **2002-08-16** - 初始版本 (0.16)

---

**注意**：本文档适用于 GCC 7.0 及以上版本。部分高级选项可能需要特定版本的 GCC 支持。建议使用最新稳定版 GCC 以获得最佳性能和最多功能支持。

**推荐版本**: GCC 11+ 或最新稳定版
