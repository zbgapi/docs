ZBG API 文档项目
-----

## 简介

本项目是ZBG API 文档项目，这里介绍下文档的结构和运行。

## 版本和结构

本项目文档是基于`slate`生成的。项目从 https://github.com/lord/slate fork而来。

由于 slate 原生不支持多语言多版本独立部署，此项目改为用 临时目录 中转部署文件的方案部署。每个语言单独一个分支，修改运行后再将生成的静态页面拷贝到`gh-pages`分支。

目前只有一个版本的API，支持英文和中文两种语言。目前我们有的分支：v1_cn, v1_en, future_cn, future_en。web页面访问
格式：https://zbgapi.github.io/docs/{module}/{version}/{language}，例如访问中文现货：https://zbgapi.github.io/docs/spot/v1/cn

## 修改样式(logo, style, layout）

1. `checkout`主分支代码
2. 修改样式文件
3. 提交推送
4. 合并和其他分支，并运行`./deploy.sh`

## 修改API文档

1. `checkout`想要修改的分支代码
2. 修改`./source/index.html.md`文件并提交
3. 并运行`./deploy.sh`
4. 确认页面效果

## slate 编辑

- 修改logo: https://github.com/lord/slate/wiki/Changing-the-Logo
- 定义样式: https://github.com/lord/slate/wiki/Custom-Slate-Themes
- 修改字体: https://github.com/slatedocs/slate/wiki/Changing-the-font
- 编写markdown：https://github.com/slatedocs/slate/wiki/Markdown-Syntax

## slate 安装部署

- 安装：https://github.com/slatedocs/slate/wiki/Installing-Slate
- 部署：https://github.com/slatedocs/slate/wiki/Deploying-Slate



欢迎有优秀 maker 策略且交易量大的用户参与长期做市商项目。如果您的ZBG账户或者ZB账户中有折合大于5BTC资产（币币和合约账户分开统计），请提供以下信息发送邮件至：

ZB Global做市商申请：sp@zbg.com；
上币申请：http://zbglobal2018.mikecrm.com/mmyA604；
提供uid（需不存在返佣关系的uid）
提供其他交易平台maker交易量截图证明（比如30天内成交量，或者VIP等级等）
简要阐述做市方法，不需要细节
做市商项目可以支持ZT抵扣、VIP、交易量相关活动以及任何形式的返佣活动

本地运行命令：

bundle exec middleman server
