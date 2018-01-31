---
layout: post
title:  "Welcome to Jekyll!"
date:   2018-01-31 15:26:52 +0800
categories: jekyll update
---

Github Pages 基于的这套 Jekyll 网站生成工具，比 WordPress 简洁好用多了。更重要的是，可以直接用 Markdown 来进行写作。

于是乎，今天干了两件事情。

1. 把之前整个 WordPress 网站导出，迁移到 github.io，使用的工具是 [WordPress to Jekyll Exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter)
  - 导出会带一个 wp-content 文件夹，包含原来网站所有的图片，可以一并提交到 github.io
  - 全局替换所有的原先域名（我的是 http://www.bennychen.cn）为空，这样可以把所有的图片链接切换到本地 wp-content 文件夹中的资源
  - 另外貌似我导出的每篇文章的时区模式是 +0000，需要全局替换为东八区 +0800，这样文章的时间才是正确的

2. 决定 2018 年要重新开始写文章，至少一周一篇。我争取尽量不写技术的。

<br>

------------

<br>

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
