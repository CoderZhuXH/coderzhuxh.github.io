---
layout: page
title: About
description: 朱晓辉技术博客
keywords: Xiaohui Zhu, 马壮
comments: true
menu: 关于
permalink: /about/
---

嗨，欢迎访问我的博客。我是一名20多岁的iOS开发者，现居深圳。 目前擅长：OC, Swift


## 坚信

* 熟能生巧
* 努力改变人生

## 联系

* GitHub：[@CoderZhuXH](https://github.com/CoderZhuXH)
* 博客：[{{ site.title }}]({{ site.url }})
* 微博: [@朱晓辉](http://weibo.com/xhwb6)
* QQ: [@Allen](http://shang.qq.com/email/stop/email_stop.html?qq=977950862)

## Skill Keywords

#### Software Engineer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_software_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Mobile Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_mobile_app_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>

#### Windows Developer Keywords
<div class="btn-inline">
    {% for keyword in site.skill_windows_keywords %}
    <button class="btn btn-outline" type="button">{{ keyword }}</button>
    {% endfor %}
</div>
