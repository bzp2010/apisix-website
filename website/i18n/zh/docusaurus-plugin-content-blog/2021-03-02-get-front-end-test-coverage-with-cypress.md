---
title: 使用赛普拉斯获得前端测试覆盖率
author: 孙毅
authorURL: "https://github.com/LiteSun"
authorImageURL: "https://avatars.githubusercontent.com/u/31329157?s=400&u=e81b4bb4db2be162c1fcac6d188f5b0f82f71920&v=4"
---

> [@LiteSun](https://github.com/LiteSun) [，来自深圳市知流科技有限公司的](https://www.apiseven.com/)Apache APISIX 提交者。

<!--truncate-->

> 来源：
>
> - https://github.com/apache/apisix
> - https://github.com/apache/apisix-dashboard

## 背景

在[“赛普拉斯稳定产品交付”一文中](/blog/2021/02/08/stable-product-delivery-with-cypress)，我们讨论了为什么选择赛普拉斯作为我们的 E2E 测试框架。在花了将近两个月的时间完善测试用例之后，我们需要测试覆盖率来量化测试覆盖率是否足够。 本文将介绍如何使用 Cypress 获取 APISIX Dashboard 前端 E2E 覆盖率。

## 什么是代码覆盖率？

代码覆盖率是软件测试中的一个指标，它描述了程序中源代码被测试的比例和程度，由此产生的比例称为代码覆盖率。测试代码覆盖率在一定程度上反映了代码的健康程度。

## 安装依赖和配置

为了收集测试覆盖率数据，我们需要在原始业务代码中放置一些探针，以便赛普拉斯收集数据。

Cypress官方推荐了两种方式，第一种是通过`nyc`生成一个临时目录，运行已经写入probe的代码来收集测试覆盖率数据。第二种方式是通过代码转换管道实时进行代码转换，免去了临时文件夹的麻烦，使得收集测试覆盖率数据相对清爽。我们选择第二种方式收集前端E2E覆盖。

1. 安装依赖

```shell
yarn add  babel-plugin-istanbul --dev
```

1. 安装cypress插件

```shell
yarn add  @cypress/code-coverage --dev
```

1. 配置 babel

```ts
// web/config/config.ts
extraBabelPlugins: [
    ['babel-plugin-istanbul',  {
      "exclude": ["**/.umi", "**/locales"]
    }],
  ],
```

1. 配置 Cypress 代码覆盖率插件

```javaScript
// web/cypress/plugins/index.js
module.exports = (on, config) => {
  require('@cypress/code-coverage/task')(on, config);
  return config;
};
```

```javaScript
// web/cypress/support/index.js
import '@cypress/code-coverage/support';
```

1. 获取测试覆盖率

配置完成后，我们需要运行测试用例。测试用例运行后，赛普拉斯将生成`coverage`和`.nyc_output`文件夹，其中包含测试覆盖率报告。

![1.png](https://lh4.googleusercontent.com/o-tyQagmCjprpNkuTjMFLaALZKtW4pyC9nj-GcPx4MM3xK0zrMED9Nndk5ZmZkZsQ5SIJPEovcrHyjWP2YXtEcYYDpLL49aV_97N83doTkOuMXlFsVjGu53A9FdlxOCr6i3aIDTA)

执行以下命令后，测试覆盖率信息将出现在控制台中。

```shell
npx nyc report --reporter=text-summary
```

![2.png](https://lh4.googleusercontent.com/n0CON1WF64wEnh3IYEc3wwwOJ2Ft_WmMLfkhOPKIKxoW0NP6Eq8VplJ87EepL5zIWOeyfJhlDmhc3ImE0ivgRlXWe1RuW2x7vL_JEri7Mz6b3tOY0it8bVvUe83CAHNgeoyXZnsy)

在覆盖目录下，将提供更详细的报告页面，如下所示。

![3.png](https://lh4.googleusercontent.com/skjR9YUcbmeytfoYnR0it7Vfc7mheCJDt7PSUsp549IbOdfqskTrIOqUXw01e0fnuNwpGoo3GtqAER3eQjNoTIdmU7HY6hc_sZ5NYc3h-MyxqmVz_NaC3AM-J4rWJFy-9IoTWjpn)

- Statements 表示每个语句是否被执行

- 分支指示每个 if 块是否被执行

- 函数指示每个函数是否被调用

- Lines 表示每一行是否被执行

## 概括

测试覆盖率在一定程度上反映了项目的质量。目前，APISIX Dashboard前端E2E覆盖率已经达到71.57%。我们将继续与社区共同努力，提升测试覆盖率，为用户提供更可靠、更稳定的产品。
