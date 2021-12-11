---
title: 用lighttpd的rewrite来做wordpress的permalink
author: kxn
type: post
date: 2005-12-05T07:20:13+00:00
url: /post/15.html
views:
  - 5707
categories:
  - Network
  - Tech Notes
---

WordPress 的 permallink 需要 apache 的支持，wordpress 会自动生成一个 .htaccess 文件来给 apache 加 rewrite 规则，但是 lighttpd 的 rewrite 规则格式和 apache 不一样，而且好像不支持 .htaccess. 　需要手动转换一下。

WordPress 生成的 .htaccess 规则大概是

RewriteBase /wordpress/  
RewriteRule ^.\*$ index.php [S=47]  
RewriteRule ^feed/(feed|rdf|rss|rss2|atom)/?$ /wordpress/index.php?&feed=$1 [QSA,L]  
RewriteRule ^(feed|rdf|rss|rss2|atom)/?$ /wordpress/index.php?&feed=$1 [QSA,L]

这样的

lighttpd 需要的 rewrite 规则大概是

url.rewrite = ("^/wordpress/feed/(feed|rdf|rss|rss2|atom)/?$" => "/wordpress/index.php?&feed=$1",  
"^/wordpress/(feed|rdf|rss|rss2|atom)/?$" => "/wordpress/index.php?&feed=$1",  
"^/wordpress/page/?([0-9]{1,})/?$" => "/wordpress/index.php?&paged=$1"  
)

这样的

内容和 Apache 的基本类似，就是把所有规则转换成一个 perl hash 声明类似的字串就可以了。  
这里可以用程序来作，但是我懒得写程序，直接用 UltraEdit 的列模式和替换就可以了：

首先删除

RewriteEngine On  
RewriteBase /wordpress/  
RewriteCond %{REQUEST_FILENAME} -f [OR]  
RewriteCond %{REQUEST_FILENAME} -d  
RewriteRule ^.\*$ index.php [S=47]

这些对 lighttpd 之类不需要的内容。

然后 UE 切换成列模式，删除所有列前面的 RewriteRule 和空格  
在最前面插入 "/wordpress/  
这样就把 RewriteBase 的内容包括进来了，如果没有 RewriteBase 的话，直接插入一个双引号就可以了

接下来用全文替换将最后的 [QSA,L] 　和之前的一个空格替换成引号和逗号 ",

现在文本里面就只剩下规则中间的那个空格了，用全文替换将这个空格替换成 "=>"

最后手动编辑一下，最后一行的逗号删掉改成 )  
第一行最前面加 url.rewrite = (

lighttpd 需要的 rewrite 规则就写完了，将这个文本的内容保存为 wprewrite.conf  
然后在 lighttpd.conf 里面 include "wprewrite.conf"

重启 lighttpd 　搞定
