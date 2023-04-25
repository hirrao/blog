---
title: 当前环境下注册OPENAI账号的一种方法
categories:
 - AI
 - OPENAI
tags: 
 - OPENAI
 - AI
date: 2023-04-24 21:00:00
---

随着OPENAI系列产品的大火，OPENAI注册和使用的要求也越来越高，比如目前大部分代理的IP地址都无法进入platform.openai.com登录账号，无法绕过Cloudflare的验证，更不用说注册账号了，对于只使用API的人来说，在使用API时OPENAI并不检测你的IP地址，因此只需要有一个账号就可以正常使用，目前购买账号价格水涨船高，并且号商批量注册的账号被封禁概率也会明显变大，自己注册账号仍然是最经济合理的选择，下面提供一种可能可行的方法来注册一个OPENAI账号

# 需要准备的内容

1. 一个[谷歌](https://mail.google.com)账号
2. 一个[阿里云](https://cn.aliyun.com)账号
3. 一个[sms-active](https://sms-activate.org/cn)账号
4. 一台电脑

## 为什么需要谷歌账号

目前OPENAI的注册逐渐收窄，只有Gmail可以稳定注册，而outlook邮箱可以概率使用，其他邮箱完全不能使用

## 为什么推荐阿里云

注册需要一个用的人少，并且和cloudflare较近的服务器，阿里云作为国内云服务商，很少有人使用阿里云服务器来提供代理服务，并且提供了印度尼西亚这种节点，可以节省成本

# 具体操作

## 购买云服务器

进入[阿里云云服务器ECS控制台](https://ecs-buy.aliyun.com/)，购买一个按量付费的实例，如
![aliyun_cloud](2023/04/当前环境下注册OPENAI账号的一种方法/aliyun_cloud.png)
建议内存至少2G，CPU一核或两核足以，因为用的时间短，完全可以使用按量付费

因为后面需要远程桌面要服务器上面去，而Linux系统不自带桌面，所以系统选择Windows server 2022，中文与英文均可，建议不要选择2022之前的版本，因为默认浏览器为IE，不支持注册，还需要自己更换浏览器

可以把默认的ESSD云盘改为高效云盘，便宜一点点

流量计费建议选择按量计费，因为本身使用不了多少上行流量，还可能抹零直接全部抹掉

我最后的配置是这样的

![config_1](2023/04/当前环境下注册OPENAI账号的一种方法/config_1.png)
![config_2](2023/04/当前环境下注册OPENAI账号的一种方法/config_2.png)

之后直接下单，创建实例后下载RDP文件，等待完全启动后直接打开RDP文件使用远程桌面连接到服务器上

## 注册账号

直接进入 https://platform.openai.com （或者 https://chat.openai.com 也行），然后点击sign up，建议直接使用邮箱注册而不是选择下方的sign in with google，之后验证邮箱（这里建议使用这个电脑注册谷歌账号，或者在自己电脑上注册后用自己电脑登录邮箱，防止谷歌账号被风控），一直到验证手机号这个环节

之后打开sms-active，购买一个印度尼西亚的手机号，此时的价格是22.5卢布，是所有手机号里面最便宜的，然后直接输入等待验证即可，如果该手机号5分钟都没有收到验证码，则退款重新购买一个，直到验证成功（这一点没有那么严格，更换几个手机号都没有触发风控）

之后通过cloudflare的验证（这一步基本上代理都是过不去的，可能还会看你的相应时间来判断是否为代理），此时正常情况下会跳转回注册前的界面，如果提示注册失败，那基本上是此时云服务器的IP有多人使用·（概率应该极低）

# 注意事项

1. 本文是仅使用APIkey的注册教程，建议注册后立刻[获取API key](https://platform.openai.com/account/api-keys)，然后创建一个新的API key，记录下来，之后建议不要在任何地点以任意形式登录OPENAI账号，减少被封号的可能
2. 不要在香港，台湾等代理下使用API key，因OPENAI在中国仅仅受到了DNS干扰，可以通过改hosts绕过，也不要在直连环境下尝试使用API key，这种情况下有很大概率被封号
3. 正常使用API key可以试试这两个扩展[ChatHub](https://chrome.google.com/webstore/detail/chathub-all-in-one-chatbo/iaakpnchhognanibcahlpcplchdfmgma)，[OpenAi Translator](https://chrome.google.com/webstore/detail/openai-translator/ogjibjphoadhljaoicdnjnmgokohngcc)