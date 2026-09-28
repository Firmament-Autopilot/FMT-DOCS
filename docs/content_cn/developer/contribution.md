

## 贡献指南

感谢您的贡献，并欢迎通过GitHub的分支和拉取请求流程提交代码。

### 编码风格

这是给FMT开发者的开发指南。作为开源软件，FMT是由不同人合作完成的。本文档是FMT开发者的指南，请在参与开发时遵循此指南。通过本文档，FMT用户也可以了解到一些代码约定，从而更容易理解FMT的实现。

#### 1. 目录命名

通常情况下，请使用小写字母命名目录。目录应具有描述性的名称。

#### 2. 文件命名

通常情况下，请使用小写字母命名文件。如果文件引用其他地方，则可以使用原始名称。为了避免命名冲突，请不要使用常见的通用名称。

#### 3. 头文件

为了避免多次包含同一个头文件，您需要定义一个符号，如下所示：

```c
#ifndef FILE_H__
#define FILE_H__
/* header file content */
#endif
```

符号应以“ _ ”结尾，以避免命名冲突。文件名的单词应以“ _ ”连接。

#### 4. 头文件注释

每个头文件中都应包含版权信息和类似以下格式的变更记录：

```c
/******************************************************************************
 * Copyright 2021-2023 The Firmament Authors. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 *****************************************************************************/
```

#### 5. 结构体定义

请将结构命名为小写，并用“_”连接单词。例如：

```c
struct mcn_list {
    McnHub_t hub;
    McnList_t next;
};
```

花括号应该单独占据一行，并且成员应该进行缩进定义。

#### 6. 宏

在 FMT 中，请使用大写字母命名宏定义。单词之间用“_”连接，例如：

```c
#define FMT_USING_CHECKED
```

#### 7. 函数命名和声明

请将函数命名为小写，并用“_”分隔单词。提供给上层应用的 API 应该在头文件中声明。如果函数没有参数，声明时应声明为 void：

```c
fmt_err_t mcn_init(void);
```

#### 8. 注释

请使用英文进行注释。注释内容应该简洁明了地描述代码的功能和复杂算法的工作原理。对语句的注释应放置在语句前或其右侧，其他位置不合法。

```c
/* your code comment */
code1();
code2(); /* your code comment */
```

#### 9. 缩进

请使用制表符或者四个空格进行缩进。推荐使用四个空格。除非有其他特殊意义，缩进应从 "{" 后面开始。

```c
if (condition) {
  /* others */
}
```

唯一的例外是 switch 语句。在 switch-case 语句中，“case” 应与 “switch” 对齐：

```c
switch (value) {
case value1:
  break;
}
```

"case" 应与 "switch" 对齐，随后的代码块应进行缩进。

#### 10. 大括号和空格

大括号不一定需要单独占据一行，可以紧随在其他语句之后。

```c
for (index = 0; index < MAX_NUMBER; index++) {
  /* others */
}
```

在表达式中，大多数二元和三元运算符与字符串之间应有空格。括号内外不应有空格，如：

```c
if (x <= y && z > 1) {
  /* other */
}
```

#### 11. 函数

函数应该遵循 K.I.S.S 原则（保持简单）。如果函数过长，应该将其拆分为更小的函数，并确保每个函数简化且易于理解。

#### 12. 使用 clang-format 自动格式化代码

我们提供了一个 clang-format 配置文件，可以自动格式化代码（需要 clang-format 版本 16.0.0 或更高版本）。

```
---
# clang-format documentation
# http://clang.llvm.org/docs/ClangFormat.html
# http://clang.llvm.org/docs/ClangFormatStyleOptions.html

# Preexisting formats:
# LLVM
# Google
# Chromium
# Mozilla
# WebKit

Language:        Cpp
BasedOnStyle:  WebKit

IndentWidth: 4
ContinuationIndentWidth: 4
AlignAfterOpenBracket: Align
AlignConsecutiveMacros:
  Enabled: true
  AcrossEmptyLines: true
  AcrossComments: true

AlignTrailingComments: true
BinPackArguments: false
BinPackParameters: true
AlignConsecutiveDeclarations: true
AlignConsecutiveAssignments: true

BraceWrapping:
  AfterClass:      true
  AfterControlStatement: false
  AfterEnum:       true
  AfterFunction:   true
  AfterNamespace:  true
  AfterObjCDeclaration: false
  AfterStruct:     true
  AfterUnion:      true
  BeforeCatch:     true
  BeforeElse:      true
  IndentBraces:    false

IncludeBlocks: Preserve
IndentPPDirectives: BeforeHash

MaxEmptyLinesToKeep: 1
PointerAlignment: Left
UseTab: Never
ColumnLimit: 0

...

```

### 贡献流程

#### 准备工作

安装 Git：需要将 Git 的目录添加到系统环境变量中。

#### Fork

将 [Firmament-Autopilot/FMT-Firmware](https://github.com/Firmament-Autopilot/FMT-Firmware) 仓库 Fork 到您的 Git 仓库中。

<p align="center">
  <img src="./figures/fork.png" width="65%">
</p>

#### 克隆

在您的仓库中，复制在您 Fork 后的仓库链接：

<p align="center">
  <img src="./figures/clone.png" width="65%">
</p>

您可以使用 `git clone` 命令将仓库复制到您的计算机上：

```
git clone [url] --recursive --shallow-submodules
```

#### 创建新分支

建议您基于主分支创建自己的开发分支，并使用以下命令创建新分支：

```
git checkout -b YourBranchName
```

例如，创建一个名为 "dev" 的分支：`git checkout -b dev`。

#### 开发过程

修复错误并提交新的功能代码。例如，假设开发者添加了一个 IMU 驱动程序：

```shell
FMT\FMT-Firmware> git status
On branch dev
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        src/driver/imu/mpu6050.c
        src/driver/imu/mpu6050.h
```

将所有更改添加到暂存区：

```shell
FMT\FMT-Firmware> git add -A
```

如果您只想将特定的文件添加到暂存区，请使用 `git add <file>` 命令：

```shell
git add src/driver/imu/mpu6050.c
```

#### 提交

将这些修改提交到本地仓库：

```
git commit -m "Describe your submission here"
```

> 注意：如果您在本地开发分支中有多个提交，请确保整理这些提交。FMT 仓库不接受超过五个提交的 Pull Request。

#### 推送到远程仓库

将修改后的内容推送到您远程仓库的分支。建议远程仓库的分支名称与本地分支名称保持一致。使用以下命令进行推送：

```
git push origin YourBranchName
```

#### 创建 Pull Request

进入您 Github 账户下的 FMT 仓库，点击 `New pull request -> Create pull request`。确保选择正确的分支。

<p align="center">
  <img src="./figures/pr.jpg" width="65%">
</p>

- 步骤 1：填写此 Pull Request 的标题

- 步骤 2：修改此 Pull Request 的描述信息

- 步骤 3：创建 pull request

#### 审查 Pull Request

一旦请求成功，FMT 的维护者可以看到您提交的代码。代码将进行审查，并在 GitHub 上填写评论。请及时查看 PR 的状态，并根据评论更新代码。

#### 合并 Pull Request

如果 Pull Request 的代码通过审查，代码将被合并到 FMT 仓库中。此时 Pull Request 成功完成。

到此为止，我们完成了一次代码贡献的流程。