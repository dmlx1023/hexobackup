---
title: git版本合并
url: 27.html
id: 27
categories:
  - git基础
date: 2018-01-30 12:56:34
tags:
---

常用命令
----

> *   合并版本（git cherry-pick \[-m parent-number\] commitId）
> *   解决继续合并（git cherry-pick --continue）
> *   停止合并 （git cherry-pick --abort）
> *   回退版本 （git reset --hard commitId）
> *   拉取代码 （git pull --rebase）
> *   切换分支 （git checkout branchId）

合并步骤
----

### 1\. 切换分支,拉取代码

> *   git checkout dev
> *   git reset --hard HEAD
> *   git pull --rebase

### 2\. 切换分支，合并代码

> *   git cherry-pick commitId

### 3\. 推送

> *   git push

常见问题
----

### 1\. 代码拉取不下来

> *   git reset --hard HEAD
> *   git clean -df
> *   git pull --rebase

### 2\. 冲突  

此时状态是_master|CHERRY-PICKING)_，打开ecplise,选择项目，右键-team-Synchronize workspace,选择冲突的文件，右键mergeTool,在解决完冲突之后，右键Mark as Merged.然后执行

> *   git cherry-pick --continue

完成本次版本拉取

注意点
---

如果这个版本是一个**merge**版本或者是**merge**版本的parent时，此时只能合并它的**merge**版本，使用命令：

> *   git cherry-pick -m parentNumber

其中parentNumber为1是代表合并基线版本，为2时代表合并另外一条线 **直接拉取parent版本会出现部分代码无法合并的情况。**