---
layout: post
title: "gitlab升级之路"
date: 2017-06-15 23:10
categories: [linux]
tags: linux
---

### gitlab升级

#### 升级依赖

- 监测当前环境
	```shell
	    bundle exec rake gitlab:env:info RAILS_ENV=production
	```

- 检测各个组件是否正常工作
	```shell
	    bundle exec rake gitlab:check RAILS_ENV=production
	```

- 备份
	```shell
	    bundle exec rake gitlab:backup:create RAILS_ENV=production
	```

- 升级git版本
    + 下载git源码，编译

- 升级ruby
    + 下载指定的版本，编译

- 升级gitlab
    + git branch -a(查看远程最新的稳定版)
    + git commit -a …
    + git checkout 最新版本

- 升级git-shell
    + 同gitlab

- 安装gitlab-git-http-server
	```shell
	    git clone https://gitlab.com/gitlab-org/gitlab-git-http-server.git
	    cd gitlab-git-http-server/
	    make(期间可能会出现flag参数什么返回错误2，到makefile中注释掉相关的参数即可)
	```

### 迁移数据库
	```shell
	#gitlab7不需要nodejs，不迁移数据库会500
	yum install nodejs
	//执行迁移
	sudo -u git -H bundle exec rake db:migrate RAILS_ENV=production
	//重建缓存
	sudo -u git -H bundle exec rake assets:clean assets:precompile cache:clear RAILS_ENV=production
	```
### 更新配置

#### 更新gitlab的nginx配置

+ 更新nginx的配置文件
	
	```shell
	cp lib/support/nginx/gitlab /etc/nginx/conf.d/gitlab.conf
	```

+ 同样，gitlab并非安装在默认目录，因此需要修改配置

	```shell
	 sed -i "s/home/usr\/local/g" /etc/nginx/conf.d/gitlab.conf
   ```

+ 更新gitlab.yml,当然，需要更改gitlab.yml中的配置，可以参考旧版本配置文件，新配置文件更改了一些结构，但大部分变量都沿用旧版本的

	```shell
	cp config/gitlab.yml.example config/gitlab.yml
	```

+ 更新database.yml

	```shell
	cp config/database.yml.mysql config/database.yml
	```

+ 更新gitlab-shell配置文件
	
	```shell
	cd /usr/local/git/gitlab-shell/ cp config.yml.example config.yml
	```

### 检测

+ 监测gitlab相关模块（主要是监测ruby、git等版本和状态）
	
	```shell
	bundle exec rake gitlab:env:info RAILS_ENV=production
	```
+ 监测所有模块是否正常,如果监测失败，会有相应的提示
	
	```shell
	bundle exec rake gitlab:check RAILS_ENV=production
	```
	