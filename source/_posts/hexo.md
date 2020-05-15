---
title: Hexo安装配置模板发布到github并绑定域名
date: 2020-05-15 09:25:00
cover: true
coverImg: https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200515013604.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60
categories: web
tags:
	- Hexo
	- web
	- 博客
	- Markdown
---

## Hexo简介

![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200515013604.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

特点是生成的静态网页可以部署到github pages等免费服务器。网站程序和发布的内容分离，你可以在本地架设hexo环境，配置主题模板插件，编辑文章后，生成静态页面发布到互联网上。和传统的wordpress之类的博客相比，对服务器的环境要求很低，而且可以利用现有的github和码云的免费服务假设网站，也可以利用阿里云oss之类的服务发布生成的静态文件，相对于传统的服务器，成本也低很多，因为是静态网站，响应速度也会快很多。



## 安装和使用

#### 依赖

1. node环境
2. `npm install -g hexo-cli`

#### 建站初始化

1. 创建一个站点，放在blog文件夹下，`hexo init blog && cd blog && npm install`
2. 修改配置文件 blog/_config.yml, [修改hexo网站配置](https://hexo.io/zh-cn/docs/configuration)

#### 启动

##### 本地启动

```
hexo s	# 启动后可以直接在本地浏览器查看效果，http://localhost:4000/ ,非必须。
```

##### 不启动直接创建文章（在hexo分支下操作）

```
hexo new "a sample article"	# 创建普通文章
hexo new page --path about/me "About me"	# 创建about me页面
# 用typora之类的外部编辑器编辑 ~/blog/source/about/me.md
hexo generate	#生成静态文件
```



### 美化

#### 预览修改

```
hexo clean && hexo g			# 清理并重新生成网站
```

#### 安装主题（themes)

1. 选择一个高质量主题 [hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)

2. `cd themes`切换到themes目录，`git clone https://github.com/blinkfox/hexo-theme-matery.git` 直接下载到主题文件夹
3. 修改hexo的`_conifg.yml`和hexo-theme-matery模板下的_config.yml, 直接照[hexo-theme-matery官方文档](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)改就好了，具体设置有以下几块
   1. 修改hexo配置，切换主题，设置分页等
   2. 新建各种功能页面，目录、标签、关于、留言板、友情链接等
   3. 修改模板`_config.yml`配置，导航菜单之类的设置
   4. 第三方插件安装和配置，代码高亮、内容搜索、中文链接转拼音、文章字数统计、emoji表情支持、rss订阅、在线聊天等
   5. 修改页脚，修改社交链接、打赏二维码图片、配置音乐播放器等

### 发布到github或者其他服务

#### 同步hexo编辑环境和文章

1. 创建仓库 githubname.github.io
2. 创建两个分支：master和hexo，master存放编译后的文章，这个只要配置在_config.yml中，之后就有程序自动发布了，hexo存放网站原始文件，便于同步到其他设备编辑，日常操作都是在hexo分支上。
3. 编辑hexo的配置文件，修改deploy项，指向自己的github page项目即可

#### 更新并发布

```
hexo clean && hexo g # 更新并生成网站，本地查看
hexo g -d	# 生成网站并发布到远程
```

#### 发布到多个节点

修改hexo的_config.yml配置，添加多个repo即可，除了git也还支持其他发布方式，比如sftp之类的

```yaml
deploy:
- type: git
  repo:
- type: git
  repo:
```



### github page绑定域名

[官方文档](https://help.github.com/cn/github/working-with-github-pages/about-custom-domains-and-github-pages)

1. 去github.com：在项目的settings > Github Pages 设置域名后，自动在项目的根目录下生成了一个`CNAME`文件
2. 去域名服务商处：添加cname记录，指向默认的github page域名，也就是 `用户名.github.io`
3. **github绑定域名后，发布一次就会覆盖掉CNAME，导致域名绑定失败**，解决办法是在source目录下新建CNAME文件，内容就是要绑定的域名，之后提交就不会清除域名绑定设置了

