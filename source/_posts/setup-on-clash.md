---
title: setup-on-clash
date: 2020-04-06 17:52:33
categories:
- setups
tags:
- proxy
---

# Setup on clash

> I super Love the cat logo for `clash` app. 

![image-20200406181607926](https://i.loli.net/2020/04/06/Xs37eyd2uoVZMip.png)

<!--more-->

### Installation

Thanks to the Developer @WhoJave @yichengchen @Dreamacro

Here are links for download. 

- [ClashXR](https://github.com/WhoJave/clashX/releases)
- [ClashX](https://github.com/yichengchen/clashX/releases)

### Setup

Here are something that we need setting up right.

1. Proxy Mode 

   > choose the mode 'rule'

2. Set as system proxy(❌ as default if you didn't tick it ✅)

   > Proxy switch

3. Start as login

   > Clash will start automatically every time the user logs in 

4. Dashboard

   > Check the setting information. 
   >
   > **Please note that Proxy local listening ports are 7890(HTTP) and 7891(Socks5) by default.**

5. Config

   from menu(config-remote-manage)

    > You have to make a new config file for your proxy info(including proxy rules, ip address, ports, key....) based on `config.yaml`
    >
    > Or you can type in your `remote` config address fetching files from agent hosts.



I'll talk about it later. 



#### Subconverter

Most of time we use service from proxy service provider(PSP).

But some of PSP don't provide remote config service but rather a `subscription link`.

> **Please make sure that don't leak the subscription link to others. It's like your only key to 'house'. Nobody wants to share their services with strangers.**

Then we need one of useful tool called `subconverter` to get a remote config address that we want.

> Subconverter: a service that can write those proxy info into a config file that clash supports.

##### Remote config address

The address is based on some rules. So let's analyze it.

Two parts: **<u>Remote host address + your subscription link (in another form)</u>**



1. ###### Remote host address

```shell
# some free hosts below
https://subto.herokuapp.com/sub?target={%TARGET%}&url={%URL%}
https://sub.ops.ci/sub?target={%TARGET%}&url={%URL%}
```

​	`	Arguments`:

- `target`: (destination app): i.e. clash, clashr, quan, quanx...

- `url`: (your proxy subscription): Process with [URLEncode](https://www.urlencoder.org/) first.

  > Look for more info at [here](https://github.com/tindy2013/subconverter)
  
  

2. ###### Subscription link convert

   Presume that your proxy subscription link is "https://sub.ssr.sh/link/xxxxxxxxxxxxxxxx?mu=1".

- Go to [urlEncode](https://www.urlencoder.org/)

![img](https://i.loli.net/2020/04/06/Kg2vOexoMXhBb8c.jpg)

Then we got a link "https%3A%2F%2Fsub.ssr.sh%2Flink%2Fxxxxxxxxxxxxxxxx%3Fmu%3D1".



3. ###### A FINAL STEP: fill %TARGET% and %URL% in Access Interface with actual values: 

```shell
1. https://subto.herokuapp.com/sub?target={%TARGET%}&url={%URL%}
2. #urlEncode
before: https://sub.ssr.sh/link/xxxxxxxxxxxxxxxx?mu=1
after: https%3A%2F%2Fsub.ssr.sh%2Flink%2Fxxxxxxxxxxxxxxxx%3Fmu%3D1
3. #Fill arguments in Access Interface with actual values.
i.e. https://subto.herokuapp.com/sub?target=clashr&url=https%3A%2F%2Fsub.ssr.sh%2Flink%2Fxxxxxxxxxxxxxxxx%3Fmu%3D1
```

Finally subscribe this link in Clash and you are done!



#### Customization

##### Proxy listening ports

> After successfully subscribe on clash, I met with many nuisances in daliy life due to `listening ports conflicts.`
>
> Clash sets local listening ports as 7890/7891 by default. However, I got used to listening ports 1086/1087.

So could I get a way to modify the ones that I feel comfortable with. - The answer is "YES".

in the dir `~/.config/clash`,

```shell
~$ ls -l  ~/.config/clash
total 9688
-rw-r--r--@ 1 gary  admin  4232607  1  1 11:01 Country.mmdb
-rw-r--r--@ 1 gary  admin      861  4  6 00:07 config.yaml
```

and there're also files i.e. `your_subscription.yaml`

- You need to revise the default ports in `config.yaml` and check whether the ports info is wanted or not as in  `your_subscription.yaml`.

![image-20200406205537108](https://i.loli.net/2020/04/06/gXO8wxbv1lpMyKJ.png)

- Restart `clash` app waiting for the modifications to take effect.



Waiting for more.

### Reference

1. [ macOS ClashXR 安装配置教程](https://ops.ci/docs/clashxr.html)
2. [SS/SSR/V2Ray订阅类型转换教程 | 万能API](https://merlinblog.xyz/wiki/api.html)

