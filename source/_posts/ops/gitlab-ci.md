---
title: Gitlab 使用指南 - CI 篇
tags:
  - 运维
  - gitlab
date: 2019-03-28 17:32:25
---

# 开发流程

![](https://upload-images.jianshu.io/upload_images/11353298-0acd8e91712f1c2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# GitLab Continuous Integration (GitLab CI/CD)

> CI: Continuous Integration：持续集成。
CD: Coninuous delivery and deployment：持续交付和部署。

Gitlab CI/CD 是以可持续方法论进行软件开发的内建工具（[continuous integration service](https://about.gitlab.com/gitlab-ci/) ）。

---

在使用 Gitlab CI 之前，我们需要先了解几个概念：

## Stage

阶段：通俗的讲就是步骤（把一件事分解成多个步骤来完成）。
从最上面的图中可以看到在 `CI` 中可能会有：`Build`、`Unit Test` 、`Integration Tests` 等多个阶段。

## Job

任务：就是我们在每个阶段具体要做的事情，而一个阶段可能会有多个任务。

## Pipelines

一条流水线 （ `pipeline`）就是 **一组** 在各个阶段执行的任务。在同一阶段的多个任务是可以并行的（如果 `Runner` 足够多的话），当全部的任务都执行成功之后流水线将会进入下一个阶段。反之，如果其中有一个任务失败，流水线的下一个阶段将不再执行。

## Runners

在 Gitlab CI 中，`Runner` 负责运行定义在 `.gitlab-ci.yml` 中的代码。`Runner` 为三种：`Shared Runners`、`Group Runners`、`Specific Runners`，分别表示全局共用 Runner、组共用 Runner、单个项目指定的 Runner。

## .gitlab-ci.yml

Gitlab CI 的配置文件，该文件声明了流水线的结构和顺序，以任务为最小单元。

文件中允许定义的元素：

1. [`image`](https://docs.gitlab.com/ce/ci/yaml/README.html#image)：docker 镜像，当 Gitlab-Runner 的类型为 docker 时，会根据该属性指定的镜像为脚本执行容器。
2. [`services`](https://docs.gitlab.com/ce/ci/yaml/README.html#services): 指定另一个 docker 镜像，主要用于提供服务层的能力，比如 `mysql` 。
3. [`before_script`](https://docs.gitlab.com/ce/ci/yaml/README.html#before_script-and-after_script)：任务执行前的钩子事件，比如一个 node 项目，我们可以在这里安装依赖。
4. [`after_script`](https://docs.gitlab.com/ce/ci/yaml/README.html#before_script-and-after_script)：任务执行后的钩子事件，当所有的任务都执行完毕之后被调用，不管任务是否执行成功。
5. [`stages`](https://docs.gitlab.com/ce/ci/yaml/README.html#stages)：定义流水线中的阶段，默认的为 `build` 、`test`、`deploy`。如果我们要在任务中指定其他的 `stage`，则需要使用该属性先申明。
6. [`cache`](https://docs.gitlab.com/ce/ci/yaml/README.html#cache)：需要缓存的文件，比如 node 项目可以把 node_modules 缓存起来。提示：可以定义在 `.gitlab-ci.yml` 顶级表示项目级别的，也可以申明在单个任务中。
7. [`variables`](https://docs.gitlab.com/ce/ci/yaml/README.html#variables)：变量，同样可以在顶级或者单个任务中申明。
8. [`pages`](https://docs.gitlab.com/ce/ci/yaml/README.html#pages)：内置的一个任务，用于上传任务执行的结果到 [Gitlab Pages](https://docs.gitlab.com/ce/user/project/pages/index.html)。
9. [`include`](https://docs.gitlab.com/ce/ci/yaml/README.html#include)：合并其他的 `.gitlab-ci.yml` 文件配置。
8. 在单个任务中申明的元素：
   1.  [`script`](https://docs.gitlab.com/ce/ci/yaml/README.html#script)：需要执行的脚本。
   2. [`stage`](https://docs.gitlab.com/ce/ci/yaml/README.html#stage)：标识该任务所属的阶段。
   3. [`tags`](https://docs.gitlab.com/ce/ci/yaml/README.html#tags)：为任务打上标签，用于选择特定的 `Runner`。
   4. [`only`](https://docs.gitlab.com/ce/ci/yaml/README.html#onlyexcept-basic)：用于表明何时**创建**该任务。
   5. [`except`](https://docs.gitlab.com/ce/ci/yaml/README.html#onlyexcept-basic)：用于表明何时**不创建**该任务。
   6. [`when`](https://docs.gitlab.com/ce/ci/yaml/README.html#when)：用于表明何时**运行**该任务。
   7. [`allow_failure`](https://docs.gitlab.com/ce/ci/yaml/README.html#allow_failure)：允许失败，该任务失败时不会影响整个流水线的结果。
   8. [`artifacts`](https://docs.gitlab.com/ce/ci/yaml/README.html#artifacts)：任务执行的结果，比如执行 `打包任务` 后的产出资源。
   9. [`dependencies`](https://docs.gitlab.com/ce/ci/yaml/README.html#dependencies)：依赖的其他任务。
   10. [`retry`](https://docs.gitlab.com/ce/ci/yaml/README.html#retry)：当任务失败时最多重试的次数。
   11. [`coverage`](https://docs.gitlab.com/ce/ci/yaml/README.html#coverage)：指定如何从任务结果中提取代码覆盖率。
   12. [`parallel`](https://docs.gitlab.com/ce/ci/yaml/README.html#parallel)：允许并行的任务实例个数。

> 请注意版本问题，每个属性对版本的要求并不一致，具体的可以点击属性查看官方文档。

# 使用 Gitlab CI

使用 Gitlab CI 服务的两种方式：
1. [Auto DevOps](https://docs.gitlab.com/ce/topics/autodevops/index.html)
2. 手动配置 CI/CD

## [Auto DevOps](https://docs.gitlab.com/ce/topics/autodevops/index.html)

Auto DevOps 是 Gitlab 11.0 推出的新功能，它提供了预定义的 CI/CD 配置，允许我们自动检测、构建、测试、发布以及监控应用。

暂未使用，待补充。

## 手动配置 CI/CD

手动配置主要就 2 个步骤，配置 `.gitlab-ci.yml` 文件，添加 Gitlab Runner 运行该文件。

---

下面，我们看看如何配置 Gitlab Runner：

## Gitlab Runner

Gitlab Runner 是一个开源项目，用来运行 `.gitlab-ci.yml` 中定义的任务并把结果返给 Gitlab。

### 安装

> 官方文档：https://docs.gitlab.com/runner/install/linux-repository.html

首先，添加离线仓库：

```
 curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
```
然后，安装最新版本：

```
sudo apt-get install gitlab-runner
```

### 注册

> 官方文档：https://docs.gitlab.com/runner/register/index.html

首先，我们进入要配置 Runner 的 Gitlab 仓库，在 `Settings -> CI / CD` 页面展开 `Runners` 配置面板可以看到已经分配的 Runner 以及注册 Runner 需要的参数：
![](https://upload-images.jianshu.io/upload_images/11353298-e900a4c97a1003cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后，我们使用 Gitlab-Runner 命名开始注册：

```
gitlab-runner register
```
执行完会该命令，终端会有交互，要求我们输入以下参数：

1. gitlab-ci coordinator url ：gitlab 服务地址
2. gitlab-ci token：上图中的 token
3. description：描述信息
4. tags：标签，对应 `.gitlab-ci.yml` 任务中配置的 tags，只有 tags 匹配的任务才会被该 Runner 执行。
5. executor：[执行器](https://docs.gitlab.com/runner/executors/README.html)，即任务脚本执行的环境

> 如果使用 docker 执行，则需要事先安装 [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/) 环境。

参数填写完毕之后，一个 Runner 便被注册成功了：

![](https://upload-images.jianshu.io/upload_images/11353298-2246a0cf9fc82160.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


我们回到 gitlab 仓库页面，然后刷新便可以看到 `_fzBq4PN` 这个 Runner 已经被注册到该项目：

![](https://upload-images.jianshu.io/upload_images/11353298-8e03c177ca9db9f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 我们可以点击 `编辑图标` 对该 Runner 进行修改。我比较喜欢把 `Run untagged jobs` 选项勾上，这样就不用每个任务都添加对应的 tags 了（因为现在涉及到的都是一些简单的流程）。


# 总结
首先，CI/CD 是一种软件开发流程，Gitlab CI/CD 是 Gitlab 为实现该流程而提供的一个内置工具（服务）。
其次，我们有 [Auto DevOps](https://docs.gitlab.com/ce/topics/autodevops/index.html) 与 `手动配置` 两种方式使用，本文讲解的主要是 `手动配置` 方式。
