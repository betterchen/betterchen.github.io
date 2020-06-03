---
layout: post
title: Go Modules对私有Git仓库的引用
date: 2020-06-03
Author: betterchen
tags: [programming, golang]
comments: true
---
商业项目中常常会引用保存于私有仓库的模块。在使用Go Module进行依赖管理时，工具默认由官方代理(https://proxy.golang.org,direct)定向代码仓库，这时是无法访问到私有库的。但通过配置[GOPRIVATE](https://golang.org/cmd/go/#hdr-Module_configuration_for_non_public_modules)环境变量，我们便可用go get命令来获取私有库中的模块了。

## 如何操作

1. 确保被引用的模块已经初始化为Go Module：

   ```shell
   go mod init <example.com/repo/module> # 初始化
   go mod tidy # 整理
   ```

2. 确保Git工具有权访问私有仓库。以GitHub为例，我们可以使用[令牌](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)或[证书](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)两种方式授权：

   ```shell
   # 使用令牌进行HTTPS调用时，先从GitHub获取token，然后在本地配置git命令行工具:
   git config --global url."https://golang:<token>@github.com".insteadOf"https://github.com"

   # 使用证书进行SSH调用时，在github配置本机ssh公钥即可
   ```

3. 配置GOPRIVATE变量

   ```shell
   # 可用逗号分隔配置多个私有仓库
   export GOPRIVATE=<example.com/repo>,$GOPRIVATE
   ```

4. 此时即可拉取私有模块：

   ```shell
   go get <example.com/repo/module>@<branch/tag>
   ```

## 结语

本文描述了如何使用GOPRIVATE访问私有仓库。同时Go mod工具也支持通过搭建代理并配置GOPROXY的方式访问，以供用户在不同场景下选择。

---
