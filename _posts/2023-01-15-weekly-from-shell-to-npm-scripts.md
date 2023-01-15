---
title: 从 shell 到 NPM scripts
description:
categories:
- weekly
tags:
---

NPM scripts 是在 package.json 中 `scripts` 中 one-liner 式的命令。使用 `npm run` 加上对应的指令就可以运行对应的脚本，例如 `npm run build` 或者简写为 `npm build`。

在运行 NPM scripts 时，会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。 NPM Scripts 的工作目录是 package.json 文件所在的目录，使用 `npm run pwd` 可以验证当前工作目录，注意此时自定义的脚本名称不能简写。NPM 脚本还可以直接调用工作目录下的脚本，`npm run runBash`。NPM 脚本也可以访问当前系统的环境变量，`npm run echoPath`。

比较特殊的一点是，npm run 新建的 Shell，会将当前目录的 node_modules/.bin 子目录加入PATH变量，执行结束后，再将PATH变量恢复原样。`npm build` 中的 vite 就是在 node_modules/.bin 里的 script。`npm install` 的时候如果 dependency 有可以执行的脚本，会自动放到 node_modules/.bin 目录。

![](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20230115084623.png)


```json
{
  "scripts": {
    "build": "tsc && vite build",
    "test": "echo \"Error: no test specified\" && exit 1",
    "pwd": "pwd",
    "runBash": "chmod 555 ./test.sh && ./test.sh",
    "echoPath": "echo ${PATH}"
  }
}
```