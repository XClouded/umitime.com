title: nodejs用fs.renameSync报错cross-device link not permitted
tags:
  - JavaScript
  - nodejs
date: 2012-08-30 12:24:17
---

平台是windows，linux下也会有这种错误。具体是在调用

fs.renameSync(oldfile, newfile);时候，报以下错误：

<pre>fs.js:340
  return binding.rename(pathModule._makeLong(oldPath),
                 ^
Error: EXDEV, cross-device link not permitted 'C:\Users\cssor.com\AppData\Local\T
emp\5dead17eab63b1909b2e2664bdc1810c'
    at Object.renameSync (fs.js:340:18)</pre>

通过google以后理解了所谓的cross-device是什么意思了，原来是从C盘重命名文件并保存到F盘就会导致这种错误，所以，貌似只能同一个盘符操作。。。