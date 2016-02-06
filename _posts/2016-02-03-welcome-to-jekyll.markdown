---
layout: post
title:  "Jekyll使用指南"
date:   2016-02-03 14:49:42 +0800
description: "你会在你的“_posts”文件夹中找到这篇文章；前去编辑并且重新编译站点，你会看到变化； 你可以用各种不同方式编译你的站点，但最常用的方式是通过运行“jekyll serve”命令，该方式会启动一个Web Server"
categories: front article
---
【简译By[武明礼](http://wumingli.github.io/about/)，见笑。。。】

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

你会在你的`“_posts”`文件夹中找到这篇文章；前去编辑并且重新编译站点，你会看到变化；
你可以用各种不同方式编译你的站点，但最常用的方式是通过运行`“jekyll serve”`命令，该方式会启动一个Web Server，并且当文件改动时可以自动生成你的站点。

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

添加新文章非常简单，在“_posts”文件夹下添加即可，需要遵守以下命名格式：`“YYYY-MM-DD-name-of-post.ext”`，文件头需要包含必要的内容。你可以查看本文的源文件，就会知道它是如何工作的了。

Jekyll also offers powerful support for code snippets:

Jekyll 还提供了强大的代码高亮支持：

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

前往 [Jekyll docs][jekyll-docs] 查看更多信息，以充分了解Jekyll的功能。查看提交：[Jekyll’s GitHub repo][jekyll-gh]。如果还有问题，可以在Jekyll Talk上提出：[Jekyll Talk][jekyll-talk]。

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
