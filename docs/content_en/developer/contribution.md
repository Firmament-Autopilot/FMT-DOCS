## Contribution Guide

We sincerely thank you for your contribution, and welcome to submit the code through GitHub's fork and Pull Request processes.

### Coding Style

This is an developing instruction for FMT developers. As an open source software, FMT is done by the cooperation of different people. This document is a guide for FMT developers and please obey it if you are. FMT users could also get to know some conventions in the code through it and thus easier to understand the implementations of FMT

#### 1. Directory Naming

In normal conditions, please name directories in lower-case. Directories should have descriptive names. 

#### 2. File Naming

In normal conditions, please name files in lower-case. If the file is referencing other places, it can have the original name. To avoid naming collision, do not use general names or the names that are frequently used.

#### 3. Header Files

To avoid include the same header file for multiple times, you need to define a symbol like this:

```c
#ifndef FILE_H__
#define FILE_H__
/* header file content */
#endif
```

The symbol should end with " _ " to avoid naming collision. The words of the file name should be connected by " _ ".

#### 4. Header File Comments

In every header file, there should be copyright information and Change Log record like this:

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

#### 5. Structure Defines

Please name structures in lower-case and connect words with "_". For example:

```c
struct mcn_list {
    McnHub_t hub;
    McnList_t next;
};
```

Braces should have their own lines and the members should be defines with indent.

#### 6. Macros

In FMT, please use upper-case names for macro definitions. Words are connected by "_". Like:

```c
#define FMT_USING_CHECKED
```

#### 7. Function Naming and Declaration

Please name functions in lower-case and separate words with "_". API provided to upper application should be declared in header files. If the function don't have parameters, it should be declared as void:

```c
fmt_err_t mcn_init(void);
```

#### 8. Comments

Please use English to comment. There shouldn't be too much comments and the comments should describe what does the code do. And it should describe how the complicated algorithm works. Comments to statements should be placed before them or right of them. Anther places are illegal.

```c
/* your code comment */
code1();
code2(); /* your code comment */
```

#### 9. Indent

Please use TAB or 4 spaces to indent. It's preferred to use 4 spaces. If no other special meanings, the indent should begin right after "{":

```c
if (condition) {
  /* others */
}
```

The only one exception is switch. In switch-case statements, "case" should be aligned with "switch":

```c
switch (value) {
case value1:
  break;
}
```

"case" is aligned with "switch", the following code block should be indented.

#### 10. Braces and Spaces

It is not necessary for the brace to occupy the whole line and it can follow other statements.

```c
for (index = 0; index < MAX_NUMBER; index++) {
  /* others */
}
```

In expressions, there should be a space between most binary and ternary operators and the strings. No spaces around(inside) parentheses, like:

```c
if (x <= y && z > 1) {
  /* other */
}
```

#### 11. Functions

Functions should be K.I.S.S. If the function is too long, you should split it into smaller ones and make each of them simplified and easy to understand.

#### 12. Use clang-format to format the code automatically

We provide a clang-format file to format the code automatically (`clang-format version 16.0.0` or higher version is required).

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

### Contribution Process

#### Preparation

Install Git: You need to add Git's directory to the system environment variable.

#### Fork

Fork the [Firmament-Autopilot/FMT-Firmware](https://github.com/Firmament-Autopilot/FMT-Firmware) repository into your git repository.

<p align="center">
  <img src="./figures/fork.png" width="80%">
</p>

#### Clone

In your repository, copy the repository links after your fork:

<p align="center">
  <img src="./figures/clone.png" width="80%">
</p>

You can use the `git clone` command to copy the repository to your PC:

```
git clone [url] --recursive --shallow-submodules
```

#### Create a New Branch

It is recommended that you create your own development branch based on the master branch, and use following commands to create a new branch:

```
git checkout -b YourBranchName
```

For example, create a branch named "dev": `git checkout -b dev`.

#### Developing

Modify bugs and submit new functional code. For example, suppose the developer adds an IMU driver:

```shell
FMT\FMT-Firmware> git status
On branch dev
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        src/driver/imu/mpu6050.c
        src/driver/imu/mpu6050.h
```

Add all changes to the temporary area:

```shell
FMT\FMT-Firmware> git add -A
```

If you only want to add some specified files to the temporary area, use other commands of `git add <file>`:

```shell
git add src/driver/imu/mpu6050.c
```

#### Commit

Submit this modification to the local repository:

```
git commit -m "Describe your submission here"
```

> Note: If there are multiple commits in the local development branch, in order to ensure that the FMT repository commit is clean, please tidy up the local commits. More than five commits are not accepted by Pull Request.

#### Push to Your Remote Repository

Push the modified content to the branch of your remote repository. It is recommended that the branch name of the remote repository be consistent with the local branch name.Use the following command to push:

```
git push origin YourBranchName
```

#### Create a Pull Request

Enter the FMT repository under your Github account and click `New pull request -> Create pull request`. Make sure you choose the right branch.

<p align="center">
  <img src="./figures/pr.jpg" width="80%">
</p>

Step 1: Fill in the title of this Pull Request

Step 2: Modify the description information of this Pull Request

Step 3：Create pull request.

#### Review Pull Request

Once the request is successful, the FMT maintainer can see the code you submitted. The code will be reviewed and comments will be filled in on GitHub. Please check the PR status in time and update the code according to the comments.

#### Merge Pull Request

If the Pull Request code is okay, the code will be merged into the FMT repository. This time Pull Request succeeded.

So far, we have completed a code contribution process.