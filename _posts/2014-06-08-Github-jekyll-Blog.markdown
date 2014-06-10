---
layout: post
title: "Mac OS X下使用Github+Jekyll搭建博客"    
categories:
- notes   
tag:
- Github
- Jekyll
- OS X
  
---  
 
# Mac OS X下使用Github+Jekyll搭建博客
毕业的差不多事落定了，这几天也闲来没事，于是又开始琢磨之前一直想弄的博客。在网上搜索一番之后，决定就用Github提供的Pages服务来搭一个简单的blog吧。
## 一. Github Pages
Github Pages是Github提供给Github用户用以介绍项目信息或者个人（或组织）信息的静态网页托管服务，免费且不限制流量。所以若要使用Github Pages服务首先需要注册一个Github Papes账号，然后就可以按照官方页面指示进行操作了。  
  
官方首页 <https://pages.github.com>  
![githubpages](http://ww3.sinaimg.cn/large/5657ca78gw1eh6sy94rivj20m80e0abo.jpg)
需要注意的是：  
- 新建的仓库名必须是**yourname.github.io**格式'yourname'是你的账户名称  
- 在新建的仓库添加一个HTML文件，文件名必须是**index.html**  
- 发布之后github会进行转换，等待十分钟左右打开**yourname.github.io**看到你刚写得**index.html**显示出来则成功
## 二. Jekyll
完成第一步之后说明你的个人页面已经开通，那么接下来怎么在此页面中搭建一个属于你自己的个人Blog呢？前面已经提到上传此仓库的代码会进行转换使之能够识别你的文件并展现在浏览器中，而其使用的就是Jekyll框架。Jekyll是Github官方开发的一个轻量级的Blog框架，只需要简单的设置就可以轻松获得一个效果不错的Blog系统。  
Jekyll官方首页 <http://jekyll.com>   
Jekyll中文首页 <http://jekyllcn.com>   

**首先来了解一下Jekyll的目录结构，方便理解整个Blog的运转过程。**  
  
    
	.
	├── _config.yml
	├── _drafts
	|   ├── begin-with-the-crazy-ideas.textile
	|   └── on-simplicity-in-technology.markdown
	├── _includes
	|   ├── footer.html
	|   └── header.html
	├── _layouts
	|   ├── default.html
	|   └── post.html
	├── _posts
	|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
	|   └── 2009-04-26-barcamp-boston-4-roundup.textile
	├── _site
	└── index.html  

具体作用可以查看：[官方文档](http://jekyllcn.com/docs/structure)。  

**在了解了Jekyll框架的目录结构之后，接下来就要着手开始搭建Blog了。通常我们都会在本地搭建好完整的项目并调试运行之后再上传至Github发布，那么首先就是需要本地安装Jekyll框架。**  
由于Jekyll是基于Ruby开发的框架，因此，在安装Jekyll之前确保电脑中已经装好了，可以在terminal键入命令，按以下操作进行。  
>检查ruby版本  
`ruby --version`  

我的Mac中已经自带装有最新的ruby2.0版   
>更新gem  
`sudo gem update --system`   
 
并不是所有人的gem都需要更新，我在更新的时候就提示为最新版  
 
>安装Jekyll  
`sudo gem install jekyll`  

这一步会自动安装Jekyll以及所需的一切依赖，但是我装这一步的时候卡了很久，报错一直是  

`
ERROR:  Could not find a valid gem 'jekyll' (>= 0), here is why:    
Unable to download data from https://rubygems.org/ - Errno::ETIMEDOUT: Operation timed out - connect(2) (https://rubygems.org/latest_specs.4.8.gz)
`  

可能是网络的原因，连续试过几次之后安装成功。然后我们新建一个文件夹作为我们Blog项目的工作目录，我建的目录放置在  
`/Users/Pacino/jekyllProject/myBlog`  
>打开建好的目录，使用如下命令新建一个Blog模板  
`jekyll new blog`  
进入blog目录，键入如下命令打开jekyll服务器  
`jekyll serve`  

终端显示图中信息，则创建成功  
![ServerRunning](http://ww3.sinaimg.cn/large/5657ca78gw1eh6sx0ueawj20m805jjsw.jpg)  
>然后打开浏览器键入  
`http://0.0.0.0:4000/`   
 
看到一个全新的小Blog模板了吗？是的，你成功了！  
接下来可以把Blog文件夹上传至https://github.com/yourname/yourname.github.io中。我使用github的Mac客户端，直接拷贝至仓库上传即可。