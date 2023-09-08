---
title: Chrome性能插件 - Lighthouse
date: 2023-09-06 17:58:27
tags:
- 原创
categories:
- 性能
comments: true
cover: cover.png
---

如何将  [Lighthouse](https://github.com/GoogleChrome/lighthouse)  集成到自己的Chrome插件

# Lighthouse

> Lighthouse是一款由Google开发的网页性能评估工具，旨在帮助开发人员检查网站的可访问性、性能和最佳实践等方面的问题。它可以通过检查页面的加载速度、页面大小、图片优化、缓存设置、JavaScript 执行时长等指标，为开发者提供详细的报告和建议，以优化网站性能。此外，Lighthouse还可以作为Chrome浏览器的扩展程序，提供实时网站分析和监测功能，帮助开发人员完善网站用户体验。

## 为什么选择lighthouse对网页评估性能

- 由Google维护
- 综合评分：加载速度、页面大小、资源优化、缓存设置、渲染性能等多个方面
- 遵循Web开发的最佳实践
- 模拟真实的用户体验

## 使用lighthouse插件

1. 打开开发者工具
2. 选择lighthouse Tab
3. 点击Analyze page load

## 使用lighthouse命令行

1. 确保已安装Node.js
2. 全局安装lighthouse
``` bash
npm install -g lighthouse
```
3. 使用lighthouse
``` bash
lighthouse <url>
```
4. 如果url是内网才能访问，可以在命令行添加参数extraHeaders
``` bash
lighthouse <url> --extra-headers "Cookie:<key>=<value>"
```

## 使用lighthouse api
1. 确保安装Node.js
2. 安装依赖lighthouse
3. 调用lighthouse
``` javascript
const puppeteer = require('puppeteer');
const lighthouse = require('lighthouse');
const { URL } = require('url');
(async() => {
    const url = url;
    const browser = await puppeteer.launch({
      headless: false,
      defaultViewport: null,
    });
    const lhr = await lighthouse(
      url,
      {
        port: (new URL(browser.wsEndpoint())).port,
        output: 'json',
        logLevel: 'info',
      }
    );
    await browser.close();
})();
```

# Chrome Extension

## 前端传递url和cookie
``` javascript
const read = () => {
    //获取当前活动窗口的url
    chrome.tabs.query({ active: true, currentWindow: true }, tabs => {
      const url = tabs[0].url
      //获取该url所有的cookies
      chrome.cookies.getAll({ url: url }, function (cookies) {
        //如果cookie存在，就转换成字符串
        if (cookies.length > 0) {
          const resList = cookies.map(item => {
            return `${item.name}=${item.value}`
          })
          const cookie = resList.join(';')
          console.log(cookie)
          //将url和cookie发送给后端处理
          pushUrl({ url: url, cookie: cookie }).then(res => {
            if (res.data.code == 0) {
              message.success('推送成功')
              setId(res.data.data)
              queryPalso('')
            }
            else {
              message.error(res.data.error)
            }
          })

        }
        //如果不存在cookie，则报错并重新加载此标签
        else {
          message.error('No Cookie')
          chrome.tabs.reload(tabs[0].id)
        }
      })
    })
  }
```
## 后端和jekins相互处理异步任务
### 透传url和cookie给jenkins
``` javascript
router.post("/push", async (ctx) => {
  const { url, cookie } = ctx.request.body;
  const id = await save(url);
  const jobParams = {
    url: url,
    cookie: cookie,
    id: id.toString(),
  };
  buildJob("pushInsights", jobParams);
  ctx.body = BaseResponse.success(id);
});
``` 
### jekins透传url和cookie给后端
``` javascript
router.get("/run/job", async (ctx) => {
  //获取id和url、cookie
  const id = ctx.query.id;
  const url = ctx.query.url;
  const cookie = JSON.stringify({ Cookie: ctx.query.cookie }).replace(
    /"/g,
    '\\"'
  );
  //调用lighthouse命令行
  const command = `lighthouse ${url} --extra-headers "${cookie}" --output=json --only-categories=performance --locale=zh-CN --quiet --chrome-flags="--headless"`;
  //解析数据，拿到关键指标
  exec(command, async (error, stdout) => {
    if (error) {
      console.error(`Error executing command: ${error}`);
    } else {
      const data = JSON.parse(stdout);
      const finalUrl = parse(data, "finalUrl");
      const score = parse(data, "categories.performance.score");
      const result = {
        "first-contentful-paint": parse(
          data,
          'audits["first-contentful-paint"]'
        ),
        "first-meaningful-paint": parse(
          data,
          'audits["first-meaningful-paint"]'
        ),
        "largest-contentful-paint": parse(
          data,
          'audits["largest-contentful-paint"]'
        ),
        "speed-index": parse(data, 'audits["speed-index"]'),
        "total-blocking-time": parse(data, 'audits["total-blocking-time"]'),
        "max-potential-fid": parse(data, 'audits["max-potential-fid"]'),
        "cumulative-layout-shift": parse(
          data,
          'audits["cumulative-layout-shift"]'
        ),
        "server-response-time": parse(data, 'audits["server-response-time"]'),
        score: score,
      };
      const plaso = {
        id: parseInt(id),
        url: finalUrl,
        result: JSON.stringify(result),
        score: parseInt(score * 100),
      };
      return await update(plaso);
    }
  });
});
``` 



