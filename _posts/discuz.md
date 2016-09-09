# mysql

## uc_server
uc_server/data/config.inc.php

# data & config 

# 改域名
http://faq.comsenz.com/viewnews-56

	proxy_set_header Host $http_host

根域名是在`/source/class/discuz/discuz_application.php` 中设定：`$_G['siteurl']`


discuz默认采用的是相对地址,并且设置了<base>标签。
下面重点讲一下<base>标签的设置过程。

我们打开template/default/common/header_common.htm这个模版文件，将会看到<base href="{$_G['siteurl']}" /> 那么$_G['siteurl']是在哪里设置的呢,我们打开文件source/class/class_core.php，查找siteurl，有一行是这么写的.

	$_G['siteurl'] = htmlspecialchars('http://'.$_SERVER['HTTP_HOST'].$sitepath.'/');

更详细的程序逻辑不在此细说，基本意思就是取得当前文件所在的路径。在本地的测试机上可能是这样的 http://localhost/x2/ 对应官方的论坛就是这样的 http://www.discuz.net/ 这个值最终出现在了页面中的<base>标签里。

为了说明如何实现绝对地址，还需要详细讲一下dz的页面是如何输出的

我们还以官方论坛首页为例进行说明,大体流程如下
[入口文件]->[初始化设置]->[模块文件]->[加载模板]->[执行模板]->[输出准备]->[正则替换]->[内容输出]
1.访问http://www.discuz.net/forum.php 加载入口文件forum.php
2.在最后一行之前的所有代码,都是做初始化的工作
3.forum.php最后一行代码，加载了模块文件，这里的$mod值为 index
require DISCUZ_ROOT.'./source/module/forum/forum_'.$mod.'.php';
复制代码
4.forum_index.php文件前面的部分都是模块文件的数据处理,取得版块信息。
5.forum_index.php最后一行执行代码 include template('diy:forum/discuz:'.$gid); 将通过discuz系统函数加载模版。
6.在function_core.php中的函数template()取得了所需的模板地址,为1_diy_forum_discuz.tpl.php，这里面是通过静态模板生成的php代码，将内容与模版结合在一起。
7.1_diy_forum_discuz.tpl.php的最后一条php语句为<?php output();?>正是这句话完成的输出准备与最终输出。
function_core.php中的函数output()，首先取得了页面内容。
8.通过函数output_replace()完成了正则替换.(我们要讲的重点也在这里)
9.最终,页面输出,大功告成。

output_replace()函数与cache_setting.php中$_G['setting']['output']的设置。
output_replace()函数的前半部分是应用域名替换，后面部分是静态化的实现。与之对应，cache_setting.php中设置$_G['setting']['output']的时候$output['str']是用来替换应用域名$output['preg']是用来实现静态化。

下面，我们进入后台->全局->域名设置->应用域名
在这里什么都不填的话，$output['str']是空的，在output_replace()中也不会进行应用域名的替换。
如果只填写了默认域名，那么相当于所有的应用都填的默认域名。$output['str']的值，经过output_replace()的替换，整个站点的链接都变成了绝对地址。因为替换规则就是将<a href="forum.php"换成了<a href="http://yourdomain/forum.php"，默认的论坛入口就是这4个 portal.php forum.php group.php home.php，如此一来实现了绝对地址，
如果只填写某一项应用的域名而不填默认，程序会将其他几个应用设置为当前域名。

做好了上面的准备工作，假如我们填写了默认域名，output_replace()将会把所有的php入口文件前面加上我们设置好的域名，从而形成绝对URL地址，下面一步是把URL地址静态化。接下来就是页面输出，呈在了浏览器中。

# flash

## upload img
http://bbs.zb7.com/forum.php?mod=viewthread&tid=469776
