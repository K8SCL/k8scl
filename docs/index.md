---
home: true
# heroImage: /img/web.png
heroText: 一起学习吧
tagline: 未来大佬进行中...
# actionText: 立刻进入 →
# actionLink: /web/
# bannerBg: auto # auto => 网格纹背景(有bodyBgImg时无背景)，默认 | none => 无 | '大图地址' | background: 自定义背景样式       提示：如发现文本颜色不适应你的背景时可以到palette.styl修改$bannerTextColor变量

features: # 可选的
    - title: Kubernetes 源码解析与开发
      details: 聚焦于 k8s 的源码学习和二次开发
      link: /k8scl/
      imgUrl: /img/k8s.png

# 文章列表显示方式: detailed 默认，显示详细版文章列表（包括作者、分类、标签、摘要、分页等）| simple => 显示简约版文章列表（仅标题和日期）| none 不显示文章列表
postList: none
simplePostListLength: 5 # 简约版文章列表显示的文章数量，默认10。（仅在postList设置为simple时生效）
hideRightBar: true # 是否隐藏右侧边栏
---

<!-- 小熊猫 -->
<img src="/img/panda-waving.png" class="panda no-zoom" style="width: 130px;height: 115px;opacity: 0.8;margin-bottom: -4px;padding-bottom:0;position: fixed;bottom: 0;left: 0.5rem;z-index: 1;">


# [关于 Kubernetes](https://kubernetes.io/zh-cn/)

##  Kubernetes 是什么？

<u><b>生产级别的容器编排系统</b></u>

> Kubernetes 也称为 K8s，是用于自动部署、扩缩和管理容器化应用程序的开源系统。
>>  它将组成应用程序的容器组合成逻辑单元，以便于管理和服务发现。Kubernetes 源自Google 15 年生产环境的运维经验

- 星际尺度
    > Google 每周运行数十亿个容器，Kubernetes 基于与之相同的原则来设计，能够在不扩张运维团队的情况下进行规模扩展。

- 处处适用
    > 无论是本地测试，还是跨国公司，Kubernetes 的灵活性都能让你在应对复杂系统时得心应手。

- 永不过时
    > Kubernetes 是开源系统，可以自由地部署在企业内部，私有云、混合云或公有云，让您轻松地做出合适的选择。

    <img src="https://cdn.staticaly.com/gh/k8scl/k8scl@master/assets/image/kubernetes.png" class="no-zoom"/>


# 关于项目

本项目成立的初衷是为了让国内小伙伴想学习，对 K8S 感兴趣，以及对其由扩展需求或者定制化需求推出的中文化简单总结，对于英语基础好的同学还是建议阅读国外原汁原味的文献比较好，也可以简单了解下此项目


## 项目特点

此项目可以直接支持以及渲染以下方式的图标和文本信息

- markdown(默认支持)
    > <img src="https://cdn.staticaly.com/gh/k8scl/k8scl@master/assets/image/md-render.png" class="no-zoom"/>
- drawio
    > <img src="https://cdn.staticaly.com/gh/k8scl/k8scl@master/assets/image/drawio-render.png" class="no-zoom"/>
- xmind
- mermaidjs

其他内容可以自行探索

## 构建

将本项目构建至本地只需要简单的 Web 知识即可

- nodejs >=16

### 二次开发（发行）

```
git clone https://github.com/K8SCL/k8scl
cd k8scl
yarn && yarn dev
```

### 本地部署 build

```
yarn build
```

然后部署自自己的服务器即可

## 贡献

对于此项目如果想要贡献或者共同参与项目，我们对成员没有要求，只要你对开源、Kubernetes 感兴趣即可

- 成为项目（社区）成员
- 提出 ISSUES

## 协议
GPL-3.0 license



