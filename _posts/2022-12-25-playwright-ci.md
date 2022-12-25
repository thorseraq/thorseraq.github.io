---
title: Playwright debug 技巧
description:
categories:
- weekly
tags:
- unit test
---

在使用 Playwright 写单测保护业务功能之前，首先要写出正确的 playwright 单测，这个环节有时候也会花费很多时间，但是好在 playwright 有不少 debug 技巧）。在本地打断点永远是最方便的方式，可以观察单步语句执行浏览器内容的变化。如果单测在本地执行没问题，但是在 CI 环境上失败，有时候仅靠日志不容易找到根本原因，这时候就可以借助回放。首先 playwright 可以配置将测试过程中浏览器的变化生成录像或者 trace 文件（录像和 trace 都可以看到浏览器的操作回放，trace 额外可以看到调用栈），默认保存在 test-results 文件夹（这个文件夹和 `playwright.config.ts` 在同一目录层级 ），为了提高单测运行效率，也可以设置仅当 retry 时再保存录像。然后可以在 github CI 在单测 job 后配置一个 upload-artifact 的 job，当单测失败时执行，将 test-results 文件夹上传。这样每次有单测失败时，在 github CI 页面就能下载到浏览器跑单测的回放了。


github CI 上传 artifact
[actions/upload-artifact (github.com)](https://github.com/actions/upload-artifact)

playwright record video
[Videos | Playwright](https://playwright.dev/docs/videos#record-video)

playwright record test trace
[Configuration | Playwright](https://playwright.dev/docs/test-configuration#record-test-trace)

playwright 设置回放输出文件夹位置
[TestConfig | Playwright (cuketest.com)](http://www.cuketest.com/playwright/docs/api/class-testconfig#test-config-output-dir)

全局 config file
[Configuration | Playwright (cuketest.com)](http://www.cuketest.com/playwright/docs/test-configuration#global-configuration)
