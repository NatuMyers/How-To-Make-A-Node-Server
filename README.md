### TAKEN FROM http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/
## I call it the JACKED Licence

<div id="main" class="site-main">

<div id="primary" class="content-area">

<div id="content" class="site-content" role="main">

<article id="post-749" class="post-749 post type-post status-publish format-standard has-post-thumbnail hentry category-programming tag-digitalocean tag-forever tag-nginx tag-nodejs tag-ubuntu">

<div class="post-thumbnail">![wpid-Screen-Shot-2015-06-03-at-5.24.39-AM.png](https://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-5-24-39-am.png?w=1038&h=414&crop=1)</div>

<header class="entry-header">

<div class="entry-meta"><span class="cat-links">[Programming](http://amodernstory.com/category/programming/)</span></div>

# Creating a Website with NodeJS and Nginx on DigitalOcean with automatic deployments using Git.

<div class="entry-meta"><span class="entry-date">[<time class="entry-date" datetime="2015-06-07T14:35:39+00:00">June 7, 2015</time>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/)</span> <span class="byline"><span class="author vcard">[Marek](http://amodernstory.com/author/dotcz/)</span></span> <span class="comments-link">[8 Comments](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/#comments)</span></div>

</header>

<div class="entry-content">

# [![nodejs](https://amodernstory.files.wordpress.com/2015/06/nodejs.png?w=22&h=12)](https://amodernstory.files.wordpress.com/2015/06/nodejs.png)Introduction

* * *

Just writing down the steps I usually take to deploy a website with NodeJS on Digital Ocean. We start off by creating a simple website, then we purchase a domain name from Enom.com, continue by spinning up a Ubuntu instance on DO, configure NodeJS and git, then we are free to go live.

<span id="more-749"></span>

[Creating the Website](#create)  
[Hosting with Digital Ocean](#hosting)  
[Setup of NodeJS and Git](#setup)  
[Source control](#git)  
[Acquiring a domain name](#dns)  
[Configuring Nginx](#nginx)  
[Conclusion](#conclusion)

*   References
    *   [Digital Ocean](https://www.digitalocean.com/?refcode=c1c4910901e6 "Digital Ocean")
    *   [Github](https://github.com "Github")
    *   [Hostgator](http://secure.hostgator.com/%7Eaffiliat/cgi-bin/affiliates/clickthru.cgi?id=dotcz12 "Hostgator")
    *   [BlueHost](http://www.bluehost.com/track/bestofhosting/ "BlueHost")
    *   [NodeJS](https://nodejs.org "NodeJS")

* * *

## Creating the Website

Let’s create a simple helloWorld website. Nothing fancy in NodeJS. If you don’t already have NodeJS installed do so. With most operating systems just head over to [NodeJS.org](https://nodejs.org "NodeJS") and download the installation file. On Ubuntu just execute **apt-get install nodejs** . Then create a directory called ‘simple’ and this will be our starting point.

    % npm init   // just enter through and leave everything at default
    % npm install express  --save       

Create a file called **index.js** containing

    var express = require('express')
      , http = require('http')
      , config = require('configuration');

    var app = express();
    var server = app.listen(config.port);
    console.log("Express server listening on port "+ config.port);

    app.get('/',index);

    function index(req,res){
       res.send('Hello ' + config.say);
    }

Also let’s create a configuration file, called configuration.js

    module.exports.port=3000;
    module.exports.say="World";

Now we can execute the application using

    % node index.js
    Express server listening on port 3000

Navigating to **[http://localhost:3000](http://localhost:3000)** we find the service running.

![running the NodeJS service](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-5-24-39-am.png?w=474)

Now with a website ready to go we need a a server to put it on and a domain name.

## Hosting with Digital Ocean

* * *

For hosting there’s a plutera of new options out there, ranging from Amazon Web Services to Google Compute. However the one I found easiest to use is [Digital Ocean](https://www.digitalocean.com/?refcode=c1c4910901e6 "Digital Ocean"). If you are a student you can get $100 free from the [Github Student Development Pack](https://education.github.com/pack "Github Student Development Pack") to use towards [Digital Ocean](https://www.digitalocean.com/?refcode=c1c4910901e6 "Digital Ocean").

![$100 Promo Code](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-07-48-am.png?w=474)

So create an account, login, and let’s create our first droplet!  
For most of the applications we’ll be fine with just a basic $5/month box.  
![BasicBox](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-09-54-am.png?w=474)  
Scrolling farther down the page we find the **Select Image** section, just pick a basic Ubuntu box. We could go Applications and find node, but it makes things a little more complicated (ironically).

![Select Ubuntu](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-10-47-am.png?w=474)

This would be the time to provide your SSH keys which can be found using:

    % cat ~/.ssh/id_rsa.pub
    ssh-rsa aspdjapsdklasjdlkadj12312o3j1d09jasdad0[1j2i123120131u30j 

Just copy and paste the printed key.

Now we are ready to deploy the droplet! Click Create Droplet!  
![Now creating the droplet](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-15-56-am.png?w=474)

Once DigitalOcean finishes creating the droplet you will be taken to the status page where all of the administration and password recovery will be done.  
![Status page](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-16-37-am.png?w=474)

Ready to connect to our server and configure it!

## Setup of NodeJS and Git

* * *

With the machine running in the cloud we can **ssh** into it. Grab the IP address from the Status page above. In my case it was **104.236.53.5**.

    % ssh root@104.236.53.5
     The authenticity of host '104.236.53.5 (104.236.53.5)' can't be established. (yes/no) yes
     Warning: Permanently added '104.236.53.5' (ECDSA) to the list of known hosts.
    Welcome to Ubuntu 15.04 (GNU/Linux 3.19.0-15-generic x86_64)
     * Documentation:  https://help.ubuntu.com/

Now we are ready to install NodeJS.

    % sudo apt-get update
    % sudo apt-get install nodejs -y
    % sudo apt-get install npm -y
    % sudo apt-get install build-essential -y
    % sudo npm install forever --global // We'll need this later .... 

We can test out our installation of NodeJS by executing

    % nodejs
    > console.log("HelloWorld");
    Hello World
    undefined

Quit by pressing CTRL+C twice.

However npm and other applications will be looking for the **node** executable, and not necessarily nodejs. We’ll have to create a symlink pointing from **node** to nodejs.

    % which nodejs
    /usr/bin/nodejs
    % ln -s /usr/bin/nodejs /usr/bin/node

No we are going to configure a Remote Git Server. This will allow us to automatically deploy applications, to both Github and the server. By doing this we can easily deploy without having to ssh into the server and have to do any additional configuration.

Installing **git** is straight forward using

    sudo apt-get install git -y

Find out where you are located and let’s configure a git server.

    % pwd
    /root
    % ls -l 
    total 0

    % mkdir -p git/simple.git
    % mkdir -p node/simple
    % mkdir node/config

    % cd git/simple
    % git init --bare

Use your favorite editor to create the post-receive file in the hooks directory.

    % vim hooks/post-receive

    #!/bin/sh
    forever stop index.js
    git --work-tree=/root/node/simple --git-dir=/root/git/simple.git checkout -f
    cp /root/node/config/simple_configuration.js /root/node/simple/configuration.js
    cd /root/node/simple
    npm install
    forever start  -o logfile.out -e logerror.out index.js

:wq //write and quit

make it executable

    chmod +x hooks/post-receive

After any push this script will clone a new copy of the repo and overwrite everything in the **/root/git/simple** directory. It will then copy the configuration.js file from the config directory into the **/root/node/simple** directory, this way we can preserve our production configuration files. Afterwards it’ll cd into the **/root/node/simple** directory and perform an **npm install** to install any dependencies and finally run **forever** with the index.js script.

Before we continue let’s create the production configuration script called simple_configuration.js

    % vim /root/node/config/simple_configuration.js

    module.exports.port=8000;
    module.exports.say="World Wide Web";

    :wq  //write and quit

## Source control

* * *

We’ll be keeping our website on Github, so create an account and a repo called **simple**.  
![simple repo. ](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-05-at-10-16-29-am.png?w=474)

First we need to create a .gitignore file so our configuration.js does not commit. We might be storing some sensitive information, like user accounts and database information.

    simple/.gitignore
        configuration.js    
        node_modules/

    If you already 'git add'ed files and they are being commited even thought they should be ignored, you can reset the git repo using __git rm -r --cached .__

Let’s make our initial commit, by going back to the terminal.

simple/

        % git init
        % git add remote origin git@github.com:marek5050/simple.git
        % git add .
        % git commit -m "Initial Commit"
        % git push --set-upstream origin master

**COOL! ** so we have uploaded something onto Github. But let’s do something better! Let’s upload it to our server too!

edit simple/.git/config and add the following lines

    [remote "live"]
        url = ssh://root@104.131.140.201:1024/root/git/simple.git
        fetch = +refs/heads/*:refs/remotes/origin/*

We should now have 2 remotes, one will be the origin (Github) and the second will be our server. Let’s try pushing again..

    % git add .
    % git commit -m "Pushing to server" 
    % git push 
     ...
    remote: express@4.12.4 node_modules/express
     ...
    remote: info:    Forever processing file: index.js
    To ssh://root@104.131.140.201/root/git/simple.git
    9ce91b6..d40a423  master -> master

Awesome let’s go check the server!!

        % ssh root@104.131.140.201
        % forever list 
         info:    Forever processes running
         data:        uid  command         script   forever pid   logfile                 uptime        
         data:    [0] UBf5 /usr/bin/nodejs index.js 14311   14316 /root/.forever/UBf5.log 0:0:1:47.978

        % cd node/simple
        % ls -l 
        -rw-r--r-- 1 root root   68 Jun  5 16:00 configuration.j
        -rw-r--r-- 1 root root  319 Jun  5 16:00 index.js
        -rw-r--r-- 1 root root  588 Jun  5 16:11 logerror.out
        -rw-r--r-- 1 root root   38 Jun  5 16:11 logfile.out
        drwxr-xr-x 3 root root 4096 Jun  5 16:00 node_modules
        -rw-r--r-- 1 root root  314 Jun  5 15:56 package.json
    % cat  node/simple/logerror.out
    % cat node/simple/logfile.out
    Express server listening on port 8000

The server is now running. We can now visit the website in our browser.  
![Public server running](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-05-at-11-28-54-am.png?w=474)

Now we want the website to be accessible via the default http **port 80**. So that when we direct the browser to **[http://104.131.140.201/](http://104.131.140.201/)** we’ll access it. We could just tell NodeJS to serve the page on port 80, but that limits the number of web sites we can host on a single machine. For this we can use a web proxy server such as Nginx or Apache. They are both identically configured, so we’ll go with **Nginx**. But before all of this can happen we’ll need to configure a **domain**. We can buy cheap **.com** or nowadays even **.whatever** for about $10\. Part of the student pack is a free domain name with [Namecheap](https://education.github.com/pack "Github Student Development Pack").

## Acquiring a domain name

We can buy domain names from a variety of hosting services such as [Hostgator](http://secure.hostgator.com/%7Eaffiliat/cgi-bin/affiliates/clickthru.cgi?id=dotcz12 "Hostgator") and [Bluehost](http://www.bluehost.com/track/bestofhosting/ "BlueHost"). But I’ve been using [Enom](https://enom.com "Enom") for quite a while so I’ll show you how to that up instead.

First search for an available domain that we can acquire.  
![search simplewebsite enom](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-44-56-pm.png?w=474)

Select an available Top Level Domain (tld,ie .ninja, .net, .com), for us it would be simplewebsite.rocks and click “add” and “checkout.”

![click checkout to purchase domain](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-47-30-pm.png?w=474)

Once the account is created and domain purchased, go and sign into your [Digital Ocean](https://www.digitalocean.com/?refcode=c1c4910901e6 "Digital Ocean"). On the top menu click **DNS**. Type in the name of the DNS you purchased and in the dropdown select the virtual machine spun up in the previous section.  
![DNS Configuration](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-00-29-pm.png?w=474)

The next screen will give you a bunch of information, from here we’ll just need the **NS** addresses. Usually they are **ns1.digitalocean.com, ns2.digitalocean.com, ns3.digitalocean.com**.

![Detailed information about DNS](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-01-35-pm.png?w=474)

Go back to [Enom](https://enom.com "Enom") and once logged in there’s a little box called **Domains** click on **Registered**. Find and select the domain and click on **DNS Server Settings.**  
Configure the Name Servers to the information we found a few seconds ago. Probably **ns1.digitalocean.com, ns2.digitalocean.com, ns3.digitalocean.com**.

![DNS Server Settings](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-57-08-pm.png?w=474)

Click save, you’ll get a message that the propagation usually takes about 24 hours. Yet, from experience it usually takes 15 seconds, so we can go on to configure Nginx.

## Configuring Nginx

* * *

Okay I ended up buying a different domain name I want to test out, **crossfit.social.**  
The installation is straight forward with a simple:

    % sudo apt-get install nginx

Now we need to configure it, the quickest way will be by creating a file crossfit.social.conf.  

<pre>%vim /etc/nginx/conf.d/crossfit.social.conf
server {
  listen 80; 
  server_name crossfit.social;

  location / {
    proxy_pass http://localhost:8000;
    proxy_http_version 1.1; 
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection ‘upgrade’;
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
</pre>

Nginx should be already running and will need to be reloaded with

    % service nginx reload

The configuration file tells nginx whenever it receives a request for **crossfit.social** it should redirect it to **localhost:8000**, which is the port our NodeJS server is running on.

![DNS Configured.. We can access the page](http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-25-57-pm.png?w=474)

## Conclusion

With forever configured our server will run almost forever and restart whenever it encounters a problem. The only thing we need to worry about now is, what happens when the virtual machine restarts? We need to create a script in the **/etc/init.d/** directory. The script will automatically run forever start /root/node/simple/index.js upon restart. This way we cover most of our corner cases. I’ll paste the script here in a week or so since school is finally starting again!

<div class="wpcnt">

<div class="wpa wpmrec">[](https://wordpress.com/about-these-ads/)

<div class="u"><script type="text/javascript">(function(g){g.__ATA.initAd({sectionId:26942, width:300, height:250});})(window);</script></div>

</div>

</div>

<div id="jp-post-flair" class="sharedaddy sd-like-enabled sd-sharing-enabled">

<div class="sharedaddy sd-sharing-enabled">

<div class="robots-nocontent sd-block sd-social sd-social-icon-text sd-sharing">

### Share this:

<div class="sd-content">

*   [<span>Google</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=google-plus-1 "Click to share on Google+")
*   [<span>Twitter</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=twitter "Click to share on Twitter")
*   [<span>Facebook</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=facebook "Share on Facebook")
*   [<span>Tumblr</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=tumblr "Click to share on Tumblr")
*   [<span>Pinterest</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=pinterest "Click to share on Pinterest")
*   [<span>LinkedIn</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=linkedin "Click to share on LinkedIn")
*   [<span>More</span>](#)

<div class="sharing-hidden">

<div class="inner" style="display: none;">

*   [<span>Pocket</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=pocket "Click to share on Pocket")
*   [<span>Reddit</span>](http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=reddit "Click to share on Reddit")

</div>

</div>

</div>

</div>

</div>

<div class="sharedaddy sd-block sd-like jetpack-likes-widget-wrapper jetpack-likes-widget-unloaded" id="like-post-wrapper-70359688-749-56cb30fba33a3" data-src="//widgets.wp.com/likes/#blog_id=70359688&amp;post_id=749&amp;origin=amodernstory.wordpress.com&amp;obj_id=70359688-749-56cb30fba33a3" data-name="like-post-frame-70359688-749-56cb30fba33a3">

### Like this:

<div class="likes-widget-placeholder post-likes-widget-placeholder" style="height:55px"><span class="button"><span>Like</span></span> <span class="loading">Loading...</span></div>

<span class="sd-text-color"></span><a class="sd-link-color"></a></div>

</div>

</div>

<footer class="entry-meta"><span class="tag-links">[digitalocean](http://amodernstory.com/tag/digitalocean/)[forever](http://amodernstory.com/tag/forever/)[nginx](http://amodernstory.com/tag/nginx/)[nodejs](http://amodernstory.com/tag/nodejs/)[ubuntu](http://amodernstory.com/tag/ubuntu/)</span></footer>

</article>

<nav class="navigation post-navigation" role="navigation">

# Post navigation

<div class="nav-links">[<span class="meta-nav">Previous Post</span>One of those great days again](http://amodernstory.com/2015/06/05/one-of-those-great-days-again/)[<span class="meta-nav">Next Post</span>Basic Javascript Testing using Mocha and Istanbul](http://amodernstory.com/2015/06/12/basic-javascript-testing-using-mocha-and-istanbul/)</div>

</nav>

</div>

</div>

</div>
