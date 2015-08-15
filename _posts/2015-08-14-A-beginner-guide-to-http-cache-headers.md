---
layout: post
title:  "A beginer guide to http cache header"
date:   2015-08-14 15:54:42
categories: http
---

在读这篇文章之前，我假设你已经有一定的网络基础，并且已经有了一定的IT从业经验，知道CDN是什么。

##Client & CDN
---

在CDN出现之前，为了节省带宽同时提升用户体验，Client一般会对站点的js, css, images等文件进行缓存。这样当用户在在第二次请求相同的文件时Client就不会再向服务器发送请求。随着 CDN（ Content Delivery Networks ）的出现，我们开始更多的关注于怎样将需要缓存的文件缓存在CDN服务器上。如果我们配置合适的话，CDN会缓存服务器上的js, css, images等文件，并且当Client向服务器发出请求的时候，CDN会选择最近的CDN服务器，并且将结果快速返回客户端，提升用户体验。

###使用缓存的痛点
---
我们使用CDN进行缓存，带来较好的用户体验的同时也有一定的副作用，当我们的服务器上的文件发生变更时，因为CDN服务器上缓存了我们的文件，所以可能我们的更改不能及时生效。


##The basic header
---
Http的Header指令包括：Cache-Control、Connection、Date、Pragma、Transfer-Encoding、Upgrade、Via，其中Cache-Control是缓存指令。这里我们主要介绍缓存指令。


请求缓存指令包括：no-cache、no-store、max-age、max-stale、min-fresh、only-if-cached
响应缓存指令包括：public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age、s-maxage

`private VS public`

`private`该响应是对于某个或者部分用户的，不应该被共享缓存处理。
`public`则表示可以被任何缓存处理。

`no-cache VS no-store`

`no-cache`表示该资源不应该被缓存，不过CDN还是会缓存该资源，不过在每一次响应Client请求时，CDN都会向origin server发送请求，获取该资源的ETAG，通过与CDN服务器上的该资源ETAG进行比较，确认该资源是否被更新（如果Etag不相同则该资源没有被更新了）。

`no-store`表示该请求和响应都不应该被存储在缓存中。

`max-age VS s-maxage`

`max-age`和`s-maxage`的单位都是秒，`max-age`表示response在多长时间内可以使用缓存中的数据，但是`max-age`对CDN无效，当收到Client请求的时候，CDN还是会返回cache中的数据。
`s-maxage`是针对CDN的，当收到Client请求时，CDN会计算将`s-maxage－(current time- update time)` 设置到response的`max-age`中，提示Client缓存有效时间。 

＊其中update time表示更新CDN从origin server更新response的时间。


`must-revalidate VS proxy-revalidate`

`must-revalidate`表示当再次请求该资源时需要向服务器请求该资源的`etag`确认该资源有没有发生变更。
`proxy-revalidate`表示CDN在接收到Client请求该资源时，需要向origin server确认该资源的`etag`是否发生变更。

`no-transform`: 为了节省带宽，我们的再请求资源时有时候会要求压缩，而`no-transform`表示取消格式转换。