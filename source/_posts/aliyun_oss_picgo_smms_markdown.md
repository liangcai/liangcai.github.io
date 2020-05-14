---
title: 使用图床工具PicGo并配置阿里云oss作为付费图床，sm.ms作为免费图床
date: 2020-05-15 09:25:00
cover: true
coverImg: https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200515013604.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60
categories: web
tags:
	- 图床
	- web
	- 博客
	- Markdown
---



### 工具 PicGo

**PicGo: 一个用于快速上传图片并获取图片URL链接的工具**

**优点: 跨平台，可扩展， 使用方便，用户量大，稳定性高**

![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514011118.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

考虑到稳定性和成本因素，使用一个付费图床和一个免费图床。使用阿里云oss作为付费图床，sm.ms作为免费图床。

#### 使用阿里云oss作为付费图床

优点：阿里云在国内服务具有明显优势，oss对象存储价格相对透明，只要注意流量消耗不被盗链（这个注意下面的节流设置），基本成本是很低的。

1. [首先注册阿里云](https://www.aliyun.com/minisite/goods?userCode=5di2tyy8)，从推广链接注册有优惠

2. 登陆后选择`存储与网络` > `对象存储OSS` > `进入对象存储面板`

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514014208.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

   3.`创建Bucket`, bucket就是仓库，你也可以创建多个仓库， 基本设置如图

   - bucket名称：随便

   - 区域：一般就近或者大城市
   - 标准存储和低频访问对博客图床来说费用没差别，这里就用标准存储
   - 读写权限，图床是对外的，所以选择公共读
   - 其他选项按图或者默认即可

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514014816.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

   4.在`权限管理` --> `Bucket授权策略` 中新增授权，根据引导创建一个子账号（也可以先在`访问控制RAM`中创建一个OSS专用的子账号），因为是用来上传图片的账号，注意权限选`读/写`。为了安全性考虑，最好是创建子账号给图床用。

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514023242.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

   5.配置PicGo插件，关键的参数就是`KeyId`和`KeySecret`

   - 存储空间名：即上面设置的Bucket名称
   - 存储区域：上面选定地域后对应的Endpoint前面部分，即oss-cn-hangzhou 或 oss-cn-shenzhen等
   - 路径：就是图片默认存储的文件夹，不填就是根目录，一般就是img或者image
   - 网址后缀，用来设定外链图片的尺寸和质量等，这里我设置`?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60` 
   - **获取KeyId和keySecret**:  将鼠标放在右上方的用户名区域，在弹出的快捷菜单中选择**accesskeys**。选择`开始使用子用户AccessKey`, 然后在认证管理标签下为专用的子用户添加用户AccessKey，创建时记得存一份到电脑上。

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514015954.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

##### 流量优化

oss以及其他同类型服务存储基本都是约等于免费，主要成本就是外链图片的流量成本。

1. 为了节约流量避免少数几个大图跑了流量，图床的图片链接加上优化参数，固定图片宽度（优化博文排版和降低图片存储大小）和降低图片质量（对视觉影响不大，对图片存储大小影响巨大）。在外链图片地址添加图片后缀`?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60`，自动拷贝的图片都是最大800宽度，标准质量60%的图片。更多的参数设置可以参考[官方图片处理指南](https://help.aliyun.com/document_detail/44686.html)，截图2mb的图片，上传仍为原始图片，加参数外链只有1~200kb了。优点是保留了原始图片，需要高清图的时候删掉外链的参数即可。

2. PicGo第三方压缩插件，直接再PicGo界面，插件设置中搜索安装即可，如果无法安装，可以点击访问插件主界面参考官方指引。和上面的外链参数可以配合使用，也可以任意使用一个，这个是上传之前压缩，缺点是会压缩原始图片，但节约了上传流量（基本不要钱）。

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514021453.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

3. 防盗链，在阿里云oss的bucket管理界面，选择权限管理，设置防盗链，指定域名可以访问即可，[详细设置文档](https://help.aliyun.com/document_detail/31869.html?spm=5176.8466010.referer.1.39c71450JNSCGM)

   ![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514021100.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

#### 使用免费的sm.ms图床

直接注册一个账号即可，注意注册页面可能有脚本无法载入，如果无法提交注册信息请先用网络工具切换一下，注册验证邮箱之后，在dashborad，点击`API Token`，创建一个token即可。



### Tips

1. 使用系统截图功能 win + shift + s截图后，点击即可简单标注，之后直接点击PicGo上传区的`剪贴板图片`就会自动上传，然后根据左侧的链接类型，直接粘贴到使用的地方即可

![](https://trycl-image-bucket.oss-cn-shenzhen.aliyuncs.com/img/20200514022403.png?x-oss-process=image/auto-orient,1/resize,m_lfit,w_800/quality,Q_60)

2. 配合同样跨平台的typora用markdown方式写日记或者博客，直接拷贝markdwon标记，可以快捷贴图
3. PicGo完整使用和配置文档：https://picgo.github.io/PicGo-Doc/zh/guide/config.html