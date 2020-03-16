---
title: hexo-backup
date: 2020-03-16 12:41:44
tags: hexo, backup
---



# hexo backup & restore



> 项目初衷：我们想要解决在不同电脑上都能维护博客或配置、发布的内容丢失可恢复的问题。
>
> 这篇文章“鸽”了有很长一段时间, 昨天晚上刚好mac-pro重装node.js，顺便走了一遍-恢复hexo博客。



blog-backup， 主要分为三部分: 备份到github上托管， 安装配置Node.js，恢复博客。

<!--more-->



### 备份到GitHub

首先，先分析hexo生成静态网页，部署到GitHub-repository的内容。

**我们不难发现**：部署的文件内容，其实是`./public`文件夹下的所有内容。事实上，插件`hexo-deploy-git`通过COPY`hexo-gernerate`后的`public`文件内容到`.deploy_git`文件夹下，然后push到远程分支`origin/master`上完成网站的部署。

所以，我们的备份思路上，不妨上传blog文件夹下**必要**的文件即可。Plus, Git可以使用不同分支管理不同的内容，因此，我们就可以考虑使用GIt的分支来备份所有的文件。

但是，**具体哪些是必要的呢**？最简单粗暴的方法，当然是备份整个文件夹。但是，身为心理洁癖，有点强迫症患者来说，始终心里觉得怪怪的。

于是，得稍微分析hexo-blog不同文件夹的作用。



#### 分析hexo-blog文件夹

先看hexo-blog文件夹下的文件结构

```bash
.
├── README.md
├── _config.yml
├── db.json
├── hello-world.md
├── node_modules
├── package-lock.json
├── package.json
├── public
├── scaffolds
├── source
├── themes
└── to-do.md

5 directories, 7 files
```

以下文件或者文件夹的作用：

- [x] `_config.yml`文件： 站点配置文件，很多功能，插件配置都需要在此页面修改。

- [ ] `node_modules`文件夹：hero-blog所依赖的模块

- [x] `package.json`文件：依赖的模块列表

- [ ] `package-lock.json`文件：依赖的模块安装记录（由npm 根据`package.json`文件而生成)

- [ ] `public`文件夹：hexo-generate 根据`public文件夹`里的.md文件而生存的静态文件

- [x] `scaffolds文件夹：`包含模版(post, page, tag等)，blog-customization可对其模版进行修改

- [x] `source`文件夹：包含所有源文件(.md)，**这是最重要的内容之一**

- [x] `themes`文件夹：所有博客的主题文件夹(其中，`landscape`是`默认主题`)，其他的主题文件夹需要`git-clone`

  > ~~⚠️：themes文件夹比较特殊，不是直接使用`git add `进行备份,因为通常themes都是由其他开发者进行开发维护，属于`git-submodule`。Git默认是直接跳过备份。因此我们需要通过`git submodule add`进行在hexo-backup分支上的`.gitmodules`的添加记录。~~
  >
  > > 那另外的 `.gitmodules` 作用又是什么呢？其实 `hexojs/hexo-starter` 是通过 Git 的 Submodule 功能来管理主题模块，本身仓库并不备份主题文件。
  > >
  > > 其实我们对于主题文件的备份有两个方案：一个方案是将其内容全部上传进行备份。一方面可以保证原主题的更新不会影响你原先配置的效果，另一方面，主题可能在进行配置的时候还需要下载一些额外的库，直接上传可以避免再次下载这些内容和配置。另一个方案是像 `hexo-starter` 仓库一样通过 `Git-submodule` 功能来管理，这样可以进行主题的更新。

相较于 Hexo 初始化使用的仓库的备份列表，它的仓库是 [hexojs/hexo-starter](https://github.com/hexojs/hexo-starter)。

其备份列表

```bash
scaffolds
source
themes
.gitignore
.gitmodules
_config.yml
package.json

```

对比一下，它抛弃了：

1. ~~`node_modules` & `package-lock.json`~~：这两部分内容，只要保留 `package.json`，执行 `npm install` 就可以下载、生成。
2. ~~`public`~~：执行 `hexo g` 即可根据源文件生成网页内容。

这些可重新生成的文件都可以不上传，因为它们只要使用特定的操作即可恢复。

因此，最终备份文件列表

```bash
scaffolds
source
themes
themes_custom/next(存放对应主题修改的备份)
.gitignore
.gitmodules
_config.yml
package.json

#这样子我们同步之后，只需要对比一下把这些配置应用过去就可以快速完成对主题的配置。
```



#### 具体操作

1. 修改`.gitignore`文件，添加忽略名单

```bash
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
package-lock.json
```

4. 执行一下命令，在本地仓库创建`hexo-backup`分支

```bash
cd hexo-blog
git init #初始化空的Git仓库 hexo-blog/.git

git checkut -b hexo-backup #创建分支并跳转该分支
git submodule add https://github.com/theme-next/hexo-theme-next.git themes/next 
#添加位于 'themes/next' 的现存仓库到索引
...

git add . 
git commit -m "init blog backup"
```

> Last but not least,
>
> git branch -m master hexo-backup #重命名默认分支master

5. 推送远程仓库

```bash
git remote add origin git@github.com:GaryLz/GaryLz.github.io.git
git push #自动创建同名的远程分支 hexo-backup(首次push的话，git push origin)

```

> 普及一点，在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking）。比如，在`git clone`的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的`master`分支自动"追踪"`origin/master`分支。
>
> Git也允许手动建立追踪关系。
>
> > ```javascript
> > git branch --set-upstream master origin/next
> > ```
>
> 上面命令指定`master`分支追踪`origin/next`分支。
>
> 选自 <u>[阮一峰-Git远程操作详解](https://www.ruanyifeng.com/blog/2014/06/git_remote.html)</u>



##### 选做

2. 我们可以删除不使用的主题 `landscape` 或者把主题路径添加到忽略列表中。

3. 创建 `themes_custom/next` 文件夹，将对主题进行的配件修改的文件拷贝一份到这里



到Github上check一下，是否备份成功。如果有必要的话，最好将hexo-backup分支替换master分支，设置为Default-branch，方便往后的备份恢复。

>  master分支就仅仅存着hexo-blog的静态网页，实在没什么必要设置为默认，而且并不会影响hexo d -g的使用。



### 安装配置Node.js

> 昨晚刚好重装Nodejs,期间过程还有前任的指引，不算太过于曲折。想起第一次弄Node.js+hexo，那其中被各种出错所支配的恐惧😱，真的是一言难尽。

篇幅有点长，不如调转另一博客，[Node.js setup on Mac](./Node.js-setup-on-Mac.md)



### 恢复博客

备份在手，装好Node.js。之后，恢复就难以相信的简单啦。

##### 恢复

在另一台电脑上，恢复就几个命令操作

```bash
mkdir hexo-blog #新建一个文件夹，恢复备份
cd hexo-blog
git clone git@github.com:GaryLz/GaryLz.github.io.git .
#克隆默认远程分支hexo-backup上的备份，如果hexo-backup分支不是默认分支，需要加flag `-b hexo-backup`

npm install #根据package.json，安装hexo-blog需要的依赖包，生成package-lock.json文件
```



##### 测试

```bash
hexo g
hexo s
hexo d

#最后测试,正常更新博客
hexo s -g
hexo d -g
```



大家搭建好博客之后，千万别忘记备份哦:<3



#### 其他问题

讲诉个亲身遇到的奇怪问题。

成功恢复后，尝试hexo-blog更新一则post,看是否功能正常。

但是我遇到一个问题：使用hexo-deploy-git成功push到origin/master分支后，会出现清空过去master分支上的所有commits。

Why?

> 其实在写博客时候，回顾deploy的原理。**事实上，插件`hexo-deploy-git`通过COPY`hexo-gernerate`后的`public`文件内容到`.deploy_git`文件夹下，然后push到远程分支`origin/master`上完成网站的部署。**
>
> 但是我又在.gitignore文件，添加了 `deploy*/` 到忽略名单。
>
> 即deploy-git的追踪并没有备份下来。所以，别的电脑恢复后，执行 `hexo d` 之类的命令，会在本地初始化一个 `.git`文件夹，新建一个新的master分支，然后push到`origin/master`，导致之前原有的`origin/master`分支被清空（替代)。



### 参考资料

1. [Mupceet-Hexo 博客备份与恢复](https://mupceet.com/2019/09/backup-hexo-blog/)

2. [segmentfault-一篇文章了解Mac上Node环境配置](https://segmentfault.com/a/1190000015416829)

3. [Mupceet-Hexo 博客搭建与主题配置（零基础版）](https://mupceet.com/2019/08/build-blog-based-on-hexo/)

