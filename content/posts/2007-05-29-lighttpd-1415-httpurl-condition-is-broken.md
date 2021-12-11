---
title: 'lighttpd 1.4.15 $HTTP[“url”] condition is broken'
author: kxn
type: post
date: 2007-05-28T16:47:07+00:00
url: /index.php/archives/172
views:
  - 2072
categories:
  - Tech Notes

---
This has been happening on my blog for a long time, but it didn't draw my attention until someone bugged me about a download link on my server yesterday. It is an $HTTP["url"] conditional dir-listing.activate statement , however it returned 404 instead of listing the directory content.

After a short investigation, I found that in configfile-glue.c

`<br />
307:  case COMP_HTTP_URL:<br />
308:      l = con->uri.path;<br />
309:      break;<br />
` 

con->uri.path is always an empty string, thus $HTTP["url"] condition is always false.

I'm not familiar with lighttpd architecture, therefore I simply changed uri.path to uri.path_raw and everything seemed to be fine. According to lighttpd trac, the uri.path statement has been there since 1.3.x, so there must be a logic change in lighttpd core code, resulting an empty uri.path at that time. I'm curious why such a serious bug has not been reported since it was introduced. Are people all busy playing with lighttpd 1.5.0?