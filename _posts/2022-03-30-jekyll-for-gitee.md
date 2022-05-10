---

title: Jekyll移植到gitee

tags: jeckll

---

本文正在修改中...

由于之前工作比较忙，且旧的网站服务器总是出问题，索性直接关掉了。为了减少没必要的维护工作，现在开始着手使用Jeckll重新搭建网站。

与WordPress相比，Jeckll结构简单，便于维护。仅需克隆原始项目、简单配置即可。

# github.io
最开始是在github上搭建的，几乎没有遇到什么问题

1. github.io站的库名必须为`用户名.github.io`

2. github.io站，可以在库设置中的`pages`页面查看相关设置。


# gitee.io
移植到gitee遇到几个问题

1. gitee的库默认为私有的，需要手动设置为开源。

2. gitee.io站的库名，必须为`用户名`

3. gitee.io需要单独开启pages服务。开启前需要提供`手持身份证照片`、`身份证正面照片`、`身份证背面照片`。

4. gitee.io站的网页不会自动刷新，必须在pages服务页手动刷新。

5. gitee.io站的文章引用的图片，不可以使用`.\`、`\_posts`作为图片地址，应在根目录下建立个类似`img`的目录，用于存放图片。

# 域名绑定
github和gitee均支持域名绑定。有以下几个方案供选择。

## 域名解析
直接将域名设置为隐形URL，并执行自己的github.io页面或gitee.io页面即可。 
该方法快速简单有效。
但也有缺点。github下https正常，而gitee仅支持http。

## 还没写完
