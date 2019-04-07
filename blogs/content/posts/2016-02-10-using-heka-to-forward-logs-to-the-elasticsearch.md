---
title: Using Heka to forward logs to the elasticsearch
author: Christiaan Baes (chrissie1)
type: post
date: 2016-02-10T09:57:24+00:00
ID: 4353
url: /index.php/uncategorized/using-heka-to-forward-logs-to-the-elasticsearch/
views:
  - 1969
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
Sometimes twitter can be useful.

Yesterday I asked how people forward their linux logs to the logstash/elasticsearch server. And got this as one of the replies. 

[<img src="/wp-content/uploads/2016/02/twitter-195x300.png" alt="twitter" width="195" height="300" class="alignnone size-medium wp-image-4354" srcset="/wp-content/uploads/2016/02/twitter-195x300.png 195w, /wp-content/uploads/2016/02/twitter.png 303w" sizes="(max-width: 195px) 100vw, 195px" />][1]

Thanks Dan Barua and James Nugent. 

I&#8217;ve been setting up an ELK (Elastisearch, Logstash and Kibana) stack since last week and having heaps of fun doing so. 

You can go to this post for the configuration [post][2] but first go [download the package][3] you need for your server and install it. If like me you have installed the deb package you will notice that heka starts running immediately.

ALl you have to do now is update the \etc\heka\conf.d\00-hekad.toml file with the configuration in the previous link. Don&#8217;t forget to change the url to your elasticsearch server. 

Also don&#8217;t forget to give read access to the syslog file and the auth.log file.

<pre>sudo chmod 666 /var/log/syslog
sudo chmod 666 /var/log/auth.log</pre>

For instance 444 might probably be enough but who knows.

You can then check the heka log in /var/log/hekad.log

And then you restart the service/daemon

<pre>sudo /etc/init.d/heka restart</pre>

Or you can reboot the server if you&#8217;re a windows admin. 

The logs will be in the logstash-* index.

 [1]: /wp-content/uploads/2016/02/twitter.png
 [2]: http://blog.arnoudvermeer.nl/post/112602966185/replacing-logstash-with-heka
 [3]: https://github.com/mozilla-services/heka/releases