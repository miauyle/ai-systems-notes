# GitHub Pages 文档站

[返回首页](../README.md) · [完整目录](../docs/README.md)

## 主题与内容源

采用与 `algorithm-notes`、`ai-storage-notes` 相同的 Jekyll + DocSteer 1.1.1，默认 aqua 配色、系统跟随深浅色及手动切换，复用其导航、分组侧栏、搜索快捷键、正文、代码复制与桌面目录。不切换框架，不增加营销动效。

`docs/` 是唯一正文源，`navigation.json` 是唯一分组目录。`_plugins/knowledge_site.rb` 构建时创建内存中的 Jekyll collection，将 GitHub Markdown 的相对文件链接转换为站点路由，保留原文件用于编辑入口。站点不输出维护规则与工程记录模板。

首页使用主题的默认外壳和设计变量，`_layouts/knowledge-map.html` 展开六个分组，`_data/core_topics.yml` 提供机制专题入口。正文路径不变，站点路由采用 `/docs/原文件名去掉扩展名/`；完整目录为 `/docs/`。新增章同步正文、原目录与导航 JSON，核心入口按需更新。

## 构建与本地预览

需要 Ruby 3.3、Bundler 2.5.22；Node 22 仅准备渲染资源和浏览器检查，不作为网站应用框架。`Gemfile.lock` 固定主题与 Jekyll 依赖，KaTeX 0.16.22、Mermaid 11.6.0 和测试用 Playwright 1.51.1 在工作流中固定直接版本。

```sh
bundle install
npm install --prefix /tmp/ai-systems-site-deps --no-audit --no-fund --ignore-scripts katex@0.16.22 mermaid@11.6.0 playwright@1.51.1
mkdir -p assets/vendor/katex assets/vendor/mermaid
cp /tmp/ai-systems-site-deps/node_modules/katex/dist/katex.min.js /tmp/ai-systems-site-deps/node_modules/katex/dist/katex.min.css assets/vendor/katex/
cp -r /tmp/ai-systems-site-deps/node_modules/katex/dist/fonts assets/vendor/katex/
cp -r /tmp/ai-systems-site-deps/node_modules/mermaid/dist/. assets/vendor/mermaid/
bundle exec jekyll serve --baseurl /ai-systems-notes
```

浏览 `http://127.0.0.1:4000/ai-systems-notes/`。准备的第三方资源、构建目录和截图已被 Git 忽略，不提交生成物。KaTeX 与 Mermaid 在页面需要时使用；站点不向 CDN 请求渲染器或字体。Mermaid 在模式变化时重新渲染；公式解析失败保留可读源码并报错，不隐藏问题。

## 验证

```sh
python tools/check_docs.py
bundle exec jekyll build --baseurl /ai-systems-notes
python tools/check_site.py
/tmp/ai-systems-site-deps/node_modules/.bin/playwright install --with-deps chromium
NODE_PATH=/tmp/ai-systems-site-deps/node_modules node tools/check_site_ui.cjs
```

CI 构建使用 Ubuntu 24.04，与固定的 Playwright 1.51.1 浏览器依赖保持兼容；不随 `ubuntu-latest` 自动迁移操作系统。

源检查验证章节与导航；产物检查验证路由、子路径、链接锚点、编辑入口及完整搜索索引。Chromium 访问全部知识页，真实渲染公式与图，检查英文/中文搜索、键盘导航、模式持久化、图的模式更新、320/390px 阅读与移动目录。`site-qa/` 保存截图和 JSON 报告；它不是知识正文或学习任务。

## 发布与权限

`.github/workflows/pages.yml` 在 PR 与 `master` 上构建并检查；失败不会部署。PR 不取得 Pages 写权限，截图证据通过 Actions artifact 提供；仅主线构建通过后，部署作业使用 Pages 与 OIDC 权限发布。

首次发布需仓库 Settings → Pages → Source 选择 **GitHub Actions**。若尚未启用或环境保护规则阻挡，保留保护规则并由维护者确认，不能把源码合并当作网站上线。工作流部署结果才是实际发布状态。

从最新 `master` 建临时分支、提 PR，核对文件及准确 head SHA 的检查通过后，依据用户已有授权合并并删除临时分支。权限边界见[仓库访问说明](github-connection.md)，内容约定见 [CONTRIBUTING.md](../CONTRIBUTING.md)。
