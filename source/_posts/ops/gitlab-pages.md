---
title: Gitlab 使用指南 - Pages 篇
tags:
  - 运维
  - gitlab
date: 2019-03-28 17:33:39
---

# 关于
   
> 官方文档：https://docs.gitlab.com/ee/user/project/pages/ 。

Gitlab Pages 使用  [GitLab Pages daemon](https://gitlab.com/gitlab-org/gitlab-pages) 服务，它是用 GO 语言实现的简单 HTTP 服务，并且可以监听外部 IP 地址以及为自定义域名和自定义证书提供支持。它通过 SNI 支持动态证书并且默认通过 HTTP2 协议发送页面。最后推荐你去看官方文档 [README](https://gitlab.com/gitlab-org/gitlab-pages/blob/master/README.md) 以便全面了解它的工作原理。
 
---

# 确认版本

 `Gitlab` 从 `8.17版本` 开始内置 `Pages` 模块，请确认 `Gitlab` 的版本是否满足要求。否则可以跟着  [官网教程](https://docs.gitlab.com/ce/administration/pages/source.html) 把 `Pages` 服务安装好。

# 启用 Pages

打开 `gitlab.rb`：

```
sudo vim /etc/gitlab/gitlab.rb
```

修改 `Pages` 配置：

```
# Pages 服务地址
pages_external_url "http://git.liuweigl.cn"
# 启用 pages
gitlab_pages['enable'] = true
```

让 `Gitlab` 使用当前配置：

```
sudo gitlab-ctl reconfigure

# stdout
> gitlab Reconfigured!
```

完了之后呢，我们需要去自己的域名运营商控制台去添加域名解析，比如我的是腾讯云：

![](https://upload-images.jianshu.io/upload_images/11353298-8e1a7f30e73b072f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果没有域名怎么办呢？参考 [Linux智能DNS服务搭建之Bind服务](https://www.imooc.com/learn/634)（为人民服务的棒棒的👷）自己搭一个呗，顺便又解锁一个技能点。

# 使用 Pages

> 官方的案例库：https://gitlab.com/pages

## 部署简单的 html  项目

1. 我们在自己的 `gitlab` 上面创建一个测试项目：`plain-html`
2. 把项目拉取到本地：

```
# 换成你自己的仓库地址
git clone git@git.liuweigl.cn:pages/plain-html.git
```
3. 在项目中新建 `public` 目录，然后添加 `index.html` 、 `style.css` 文件:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="generator" content="GitLab Pages">
    <title>Plain HTML site using GitLab Pages</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <div class="navbar">
      <a href="https://pages.gitlab.io/plain-html/">Plain HTML Example</a>
      <a href="https://gitlab.com/pages/plain-html/">Repository</a>
      <a href="https://gitlab.com/pages/">Other Examples</a>
    </div>

    <h1>Hello World!</h1>

    <p>
      This is a simple plain-HTML website on GitLab Pages, without any fancy static site generator.
    </p>
  </body>
</html>
```

```css
body {
  font-family: sans-serif;
  margin: auto;
  max-width: 1280px;
}

.navbar {
  background-color: #313236;
  border-radius: 2px;
  max-width: 800px;
}

.navbar a {
  color: #aaa;
  display: inline-block;
  font-size: 15px;
  padding: 10px;
  text-decoration: none;
}

.navbar a:hover {
  color: #ffffff;
}
```
4. 在项目根目录添加 `.gitlab-ci.yml` 文件：

```yml
# This file is a template, and might need editing before it works on your project.
# Full project: https://gitlab.com/pages/plain-html
pages:
  tags:
    - html
  stage: deploy
  script:
    - mkdir .public
    - cp -r * .public
    - mv .public public
  artifacts:
    paths:
      - public
  only:
    - master
```
5. 提交代码：

```
git add .
git commit -m"feat(pages):  开启 pages 服务"
git push
```
最后我们回到 gitlab 服务，在 `plain-html` 仓库中的 `Settings / pages` 页面可以看到已经有对应的服务地址了：

![](https://upload-images.jianshu.io/upload_images/11353298-cdf4c39d809d9d38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

# 温馨提示

1. Have an exclusive root domain for serving GitLab Pages. Note that you cannot
use a subdomain of your GitLab's instance domain.
> 请为 Gitlab Pages 提供一个单独的域名，不要使用 Gitlab 服务的子域名哦。

---

2. You should strongly consider running GitLab pages under a different hostname
than GitLab to prevent XSS attacks.   
> gitlab 服务的域名跟 pages 的不要使用同一个，防止 XSS 攻击。
