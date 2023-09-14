---
title: 强大的前端性能测试工具-Lighthouse
comments: true
cover: cover.png
date: 2023-03-14 10:34:34
categories:
- 测试工具
keyword: lighthouse,性能测试,Chrome插件
tags:
- 原创
---

<font color='green' size='4'>Lighthouse</font> 是一个开源的自动化网页性能监测工具，由 Google 开发和维护。它可以帮助开发者评估和优化他们的网页性能，提供关于网页加载速度、性能指标和最佳实践的详细报告。
<!-- more -->

# Lighthouse
> Lighthouse 使用 Chrome 浏览器（或 Chromium）内置的性能分析工具来模拟用户访问网页并收集性能数据。它对网页进行一系列的评估，包括加载速度、资源优化、渲染性能、可访问性和最佳实践等方面。

## 在开发者工具上使用lighthouse
<font color='red'>lighthouse是chrome插件，所以只能在chrome浏览器上使用</font>

1. 打开F12开发者模式
![开发者模式](devtools.png)

2. 切换到Lighthouse Tab
![切换](changetab.png)

3. 点击<font color='blue'>Analyze page load</font>

*Mode*
- **Navigation（导航模式）**：这是默认的 Lighthouse 模式。在导航模式下，Lighthouse 将模拟用户访问网页的完整过程，包括加载 HTML、执行 JavaScript、加载资源、渲染页面等。它会收集整个过程的性能数据，并生成相应的报告。导航模式适用于评估网页的全局性能和用户体验。
- **Timespan（时间段模式）**：在时间段模式下，Lighthouse 可以针对指定的时间段进行性能分析。你可以通过设置起始时间和结束时间来定义时间段，然后 Lighthouse 会分析该时间段内的网页性能。时间段模式适用于分析网页在特定时间范围内的性能变化、识别因特定事件或请求而引起的性能问题等。
- **Snapshot（快照模式）**：在快照模式下，Lighthouse 会对当前网页的状态进行快照捕捉，而不模拟完整的用户访问过程。它会记录网页的 DOM 结构、资源情况和性能数据，并生成相应的报告。快照模式适用于评估网页的静态性能、资源利用情况和各个指标的当前状态。

*Device*
- **Mobile**：以移动端设备模拟
- **Desktop**：以桌面端设备模拟

*Categories*
- **Performance（性能）**：评估网页的加载速度和性能方面的指标，包括首次内容绘制时间、主要资源完成加载时间、总体加载时间、网络请求数量等。
- **Accessibility（可访问性）**：评估网页的可访问性方面的指标，包括是否符合 WCAG 2.0 标准、是否提供足够的文本替代描述、是否有可缩放的字体大小等。
- **Best Practices（最佳实践）**：评估网页是否遵循 Web 开发的最佳实践，包括正确使用 HTTPS、避免重定向链、禁用过时的 API 等。
- **SEO（搜索引擎优化）**：评估网页在搜索引擎优化方面的指标，包括是否具有描述性的页面标题和元描述、是否有有效的 robots.txt 文件、是否有结构化数据等。
- **Progressive Web App（渐进式 Web 应用）**：评估网页是否符合渐进式 Web 应用的标准，包括是否具有响应式设计、是否支持离线访问、是否有推送通知功能等。

4. 查看报告
![查看报告](report.png)

*Performance(性能)*

这个部分提供了关于网页加载性能方面的信息。它包括各种性能指标和数据，比如首次内容绘制时间、主要资源完成加载时间、总体加载时间、网络请求数量等。Performance 部分的目标是评估网页的整体加载性能。
Performance评分标准根据指标和对应的权重计算出相应的得分，每个版本的评分标准都不一样。得分越高表示网页的性能越好。
![评分标准](score.png)

*Metrics(指标)*

这个部分展示了一些关键的性能指标。它提供了一组可视化图表，帮助你更直观地了解网页的性能表现。例如，Time to First Byte（首字节时间）、First Contentful Paint（首次内容绘制时间）、Largest Contentful Paint（最大内容绘制时间）等。
- **First Contentful Paint（首次内容绘制时间）**：标记页面上第一个像素渲染的时间点。
- **Speed Index（速度指数）**：表示页面内容绘制的整体速度，该指标越小越好。
- **Largest Contentful Paint（最大内容绘制时间）**：页面上最大元素（例如图片或视频）的渲染时间。
- **Total Blocking Time（总阻塞时间）**：指页面在加载过程中由于 JavaScript 执行而导致主线程被阻塞的总时间。
- **Cumulative Layout Shift（累计布局偏移）**：衡量页面加载过程中元素布局发生的不稳定变化对用户体验的影响程度。

*Opportunities(优化机会)*

这个部分列出了一些可以改进网页性能的具体建议。它会识别出潜在的优化机会，并给出针对每个机会的详细说明和建议。通过优化这些方面，可以提升网页的性能和用户体验。
一般会涉及以下方面：
- **资源优化**：例如压缩和缓存静态资源、减少文件大小、延迟加载等。
- **渲染性能**：例如减少 JavaScript 和 CSS 的执行时间、避免强制布局重排等。
- **网络性能**：例如使用缓存、启用压缩、减少请求次数等。
- **用户体验**：例如改善页面的可操作性、改进互动元素的响应性等。

*Diagnostics(诊断信息)*

这个部分提供了一些有关网页的诊断信息。它会检查一些可能存在的问题或错误，并生成相应的报告。诊断信息可以帮助你发现潜在的性能障碍、兼容性问题或其他需要解决的具体情况。
一般会包含以下方面：
- **Unused JavaScript（未使用的 JavaScript）**：检测到页面中存在未使用的 JavaScript 代码，这可能会导致不必要的网络请求和执行开销。
- **Render Blocking Resources（阻塞渲染的资源）**：识别了阻塞页面渲染的资源，使页面加载速度受到影响。
- **Duplicate ID（重复的 ID）**：发现有多个元素使用了相同的 ID，可能导致文档结构混乱和操作异常。
- **Slow Server Response Time（服务器响应时间过慢）**：显示服务器响应时间超过了建议的阈值，可能导致页面加载缓慢。
- **Invalid HTML（无效的 HTML）**：指出页面存在无效或不符合规范的 HTML 代码，可能影响页面的解析和渲染。

*Passed Audits(通过的审计)*

这个部分列出了 Lighthouse 审计中通过的项目和指标。每个项目都会根据一组规则进行评估，如果满足规则要求，则被标记为 "Passed"。通过的审计意味着你的网页在某些方面是符合性能和最佳实践要求的。
一般会包含以下方面：
- **Minify CSS（压缩 CSS）**：确认 CSS 文件已经进行了压缩，以减少文件大小并提高加载速度。
- **Minify JavaScript（压缩 JavaScript）**：验证 JavaScript 文件已经进行了压缩，以减少文件大小并加速加载。
- **Image Alt Text（图像替代文本）**：检查图像元素是否包含了适当的替代文本，以提高可访问性和屏幕阅读器的使用体验。
- **Color Contrast（颜色对比度）**：评估页面中的文本和背景颜色是否具有足够的对比度，以确保易读性和可访问性。
- **No Broken Links（无损坏链接）**：确认页面不包含损坏的链接，以避免用户浏览过程中遇到错误。
- **Viewport Meta Tag（视口元标签）**：检查页面是否包含了适当的视口元标签，以确保在移动设备上正确显示。

5. 根据上面的建议进行调优

## 直接使用lighthouse cli
### 全局安装lighthouse
```bash
npm install -g lighthouse
```
### 简单使用
```bash
lighthoouse <url>
```
### 使用参数
1. --output=json，以json格式输出结果
2. --output-path=./report.json，保存结果到文件
3. --chrome-flags="--headless" ，不显式打开浏览器运行
4. --extra-headers "{\"Cookie\":\"monster=blue\", \"x-men\":\"wolverine\"}" ，带入header
5. --only-categories=performance，只返回性能相关报告
6. --quiet，静默运行

<font color='green'>其他参数可以参考：</font>*https://github.com/GoogleChrome/lighthouse*

### 查看报告
![查看报告](lighthouse-cli.png)

## 使用node api

### 安装lighthouse、启动chrome浏览器依赖
```bash
npm install lighthouse chrome-launcher --save
```

### 引入Javascript
```javascript
const fs = require('fs');
const lighthouse = require('lighthouse');
const chromeLauncher = require('chrome-launcher');   //chrome-launcher 用于调起浏览器
(async () => {
  const chrome = await chromeLauncher.launch({chromeFlags: ['--headless']});   //chromeLauncher 工具包提供了 launch 方法，
  //lighthouse运行的一些配置信息
  const options = { output: 'json',  port: chrome.port};
  //执行lighthouse得到执行结果，执行结果是一个JS对象
  const res= await lighthouse('https://www.baidu.com', options);
  //通过lighthouse的运行结果的report属性值拿到运行报告，运行报告是用于 HTML/JSON/CSV 输出结果的一个字符串
  const report = res.report;
  //生成结果文件
  fs.writeFileSync('report.json', report);
  //关闭Chrome浏览器进程
  await chrome.kill();
})();
```

<font color='red'>lighthouse api不仅支持javascript，同时也支持以google接口的形式进行请求</font>
*https://developers.google.com/speed/docs/insights/v5/reference/pagespeedapi/runpagespeed?hl=zh-cn*

