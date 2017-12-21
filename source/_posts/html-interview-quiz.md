---
title: html 前端面试题汇总
date: 2017-12-21 02:16:24
categories: web前端
tags: 
  - web前端
  - html
  - html5
  - 面试
thumbnail: /2017/12/21/html-interview-quiz/thum.jpg
banner: /2017/12/21/html-interview-quiz/thum.jpg
---
个人收集的 web 前端 html 面试题, 为来年春招做准备, 仅供参考.
<!--more-->


<h5 style="color: #949494">2017/12/21</h5>
#### 1. &lt;meta&gt; 标签与其作用
更详细请参考 [&lt;meta&gt; - HTML（超文本标记语言） | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta)

##### 属性

`charset`

声明文档所使用的字符编码
  * 默认 ASCII (推荐 UTF-8)

`content`

  为 http-equiv 或者 name 提供值

`http-equiv`
  预定义 http 响应头

枚举值：

  - "content-security-policy"内容安全策略
    详情参考 [content-security-policy | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy).

  - "content-type"
    指定默认的响应 MIME 类型

  - "default-style"
    规定要使用的预定义的样式表。(其 content 的值必须是同一文档下某个 link 的 title 值)

  - "refresh"
    定义文档自动刷新的时间间隔（个人不清楚有什么用）

`name`

不可与 charset, itemprop, http-equiv 同时使用于一个 meta 之上

枚举值:

- application-name:
  定义运行在网页上的具体应用名称，与 title 不同

- author:
  文档作者

- desscription:
  关于页面内容的简短描述

- generator:
  表明生成这个页面的应用程序

- keywords:
  描述网站的关键字

- referrer:
  控制所有从该文档发出的 HTTP 请求中HTTP Referer 首部的内容：
  ```html
  <meta name="referrer">
  ```
  > 动态插入 referrer 是没有作用的，定义冲突会采用 no-referer 策略

  content 属性可取的值：

|||
| :-: | :-: |
| no-referrer | 不要发送 HTTP Referer 首部。 |
| origin | 发送当前文档的 origin。 |
| no-referrer-when-downgrade | 当目的地是先验安全的(https->https)则发送 origin 作为 referrer ，但是当目的地是较不安全的 (https->http)时则不发送 referrer 。这个是默认的行为。 |
| origin-when-crossorigin | 在同源请求下，发送完整的URL (不含查询参数) ，其他情况下则仅发送当前文档的 origin。 |
| unsafe-URL | 在同源请求下，发送完整的URL (不含查询参数)。 |

- viewport
  [viewport 控制布局 | MDN](https://developer.mozilla.org/zh-CN/docs/Mobile/Viewport_meta_tag)
  属性值：

|value|可能值|描述|
|:-:|:-|:-|
|width|一个正整数或者字符串|device-width	defines the width, in pixels, of the viewport|
|height|一个正整数或者字符串|device-height defines the height, in pixels, of the viewport|
|initial-scale|一个0.0 到10.0之间的正数|defines the ratio between the device width (device-width in portrait mode or device-height in landscape mode) and the viewport size.|
|maximum-scale|一个0.0 到10.0之间的正数|defines the maximum value of the zoom; it must be greater or equal to the minimum-scale or the behavior is indeterminate.|
|minimum-scale|一个0.0 到10.0之间的正数|defines the minimum value of the zoom; it must be smaller or equal to the maximum-scale or the behavior is indeterminate.|
|user-scalable|一个布尔值（yes 或者no）|If set to no, the user is not able to zoom in the webpage. Default value is yes.|

- robots:
  定义爬虫如何索引以及处理该页面

##### 总结

meta 标签为文档定义一系列元数据，预定义浏览器的行为，对 SEO 优化有很大帮助。

#### 2. SEO 优化需要注意的地方有什么

- 合理的title、description、keywords：搜索对着三项的权重逐个减小，title值强调重点即可，重要关键词出现不要超过2次，而且要靠前，不同页面title要有所不同；description把页面内容高度概括，长度合适，不可过分堆砌关键词，不同页面description有所不同；keywords列举出重要关键词即可
- 语义化的HTML代码，符合W3C规范：语义化代码让搜索引擎容易理解网页
- 重要内容HTML代码放在最前：搜索引擎抓取HTML顺序是从上到下，有的搜索引擎对抓取长度有限制，保证重要内容一定会被抓取
- 重要内容不要用js输出：爬虫不会执行js获取内容
- 少用iframe：搜索引擎不会抓取iframe中的内容
- 非装饰性图片必须加alt
- 提高网站速度：网站速度是搜索引擎排序的一个重要指标

#### 3. DOCTYPE
- `<!doctype>`声明必须处于HTML文档的头部，在`<html>`标签之前，HTML5中不区分大小写
- `<!doctype>`声明不是一个HTML标签，是一个用于告诉浏览器当前HTMl版本的指令
- 现代浏览器的html布局引擎通过检查doctype决定使用兼容模式还是标准模式对文档进行渲染，一些浏览器有一个接近标准模型。
- 在HTML4.01中`<!doctype>`声明指向一个DTD，由于HTML4.01基于SGML，所以DTD指定了标记规则以保证浏览器正确渲染内容
- HTML5不基于SGML，所以不用指定DTD，使用 `<!DOCTYPE html>` 就可以声明