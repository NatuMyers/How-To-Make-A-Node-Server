### TAKEN FROM http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/
## I call it the JACKED Licence
	<div id="main" class="site-main"> 

	<div id="primary" class="content-area">
		<div id="content" class="site-content" role="main">
			 
<article id="post-749" class="post-749 post type-post status-publish format-standard has-post-thumbnail hentry category-programming tag-digitalocean tag-forever tag-nginx tag-nodejs tag-ubuntu">
	  
	<div class="post-thumbnail">
	<img width="1038" height="414" src="https://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-5-24-39-am.png?w=1038&amp;h=414&amp;crop=1" class="attachment-twentyfourteen-full-width size-twentyfourteen-full-width wp-post-image" alt="wpid-Screen-Shot-2015-06-03-at-5.24.39-AM.png" />	</div>

	 
	
	<header class="entry-header">
				<div class="entry-meta">
			<span class="cat-links"><a href="http://amodernstory.com/category/programming/" rel="category tag">Programming</a></span>
		</div>
		<h1 class="entry-title">Creating a Website with NodeJS and Nginx on DigitalOcean with automatic deployments using Git.</h1>
		<div class="entry-meta">
			<span class="entry-date"><a href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/" rel="bookmark"><time class="entry-date" datetime="2015-06-07T14:35:39+00:00">June 7, 2015</time></a></span> <span class="byline"><span class="author vcard"><a class="url fn n" href="http://amodernstory.com/author/dotcz/" rel="author">Marek</a></span></span>			<span class="comments-link"><a href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/#comments">8 Comments</a></span>
					</div><!-- .entry-meta -->
	</header><!-- .entry-header -->

		<div class="entry-content">
		<h1 id="intro"><a href="https://amodernstory.files.wordpress.com/2015/06/nodejs.png"><img class=" wp-image-895 alignleft" src="https://amodernstory.files.wordpress.com/2015/06/nodejs.png?w=22&#038;h=12" alt="nodejs" width="22" height="12" /></a>Introduction</h1>
<hr />
<p>Just writing down the steps I usually take to deploy a website with NodeJS on Digital Ocean. We start off by creating a simple website, then we purchase a domain name from Enom.com, continue by spinning up a Ubuntu instance on DO, configure NodeJS and git, then we are free to go live.</p>
<p><span id="more-749"></span></p>
<p><a href="#create">Creating the Website</a><br />
<a href="#hosting">Hosting with Digital Ocean</a><br />
<a href="#setup">Setup of NodeJS and Git</a><br />
<a href="#git">Source control</a><br />
<a href="#dns">Acquiring a domain name</a><br />
<a href="#nginx">Configuring Nginx </a><br />
<a href="#conclusion">Conclusion</a></p>
<ul>
<li>References
<ul>
<li><a title="Digital Ocean" href="https://www.digitalocean.com/?refcode=c1c4910901e6" target="_blank">Digital Ocean</a></li>
<li><a title="Github" href="https://github.com" target="_blank">Github</a></li>
<li><a title="Hostgator" href="http://secure.hostgator.com/%7Eaffiliat/cgi-bin/affiliates/clickthru.cgi?id=dotcz12" target="_blank">Hostgator</a></li>
<li><a title="BlueHost" href="http://www.bluehost.com/track/bestofhosting/" target="_blank">BlueHost</a></li>
<li><a title="NodeJS" href="https://nodejs.org" target="_blank">NodeJS</a></li>
</ul>
</li>
</ul>
<hr />
<h2 id="create">Creating the Website</h2>
<p>Let’s create a simple helloWorld website. Nothing fancy in NodeJS. If you don’t already have NodeJS installed do so. With most operating systems just head over to <a title="NodeJS" href="https://nodejs.org" target="_blank">NodeJS.org</a> and download the installation file. On Ubuntu just execute <strong>apt-get install nodejs</strong> . Then create a directory called ‘simple’ and this will be our starting point.</p>
<pre><code>% npm init   // just enter through and leave everything at default
% npm install express  --save       
</code></pre>
<p>Create a file called <strong>index.js</strong> containing</p>
<pre><code>var express = require('express')
  , http = require('http')
  , config = require('configuration');

var app = express();
var server = app.listen(config.port);
console.log("Express server listening on port "+ config.port);

app.get('/',index);

function index(req,res){
   res.send('Hello ' + config.say);
}
</code></pre>
<p>Also let’s create a configuration file, called configuration.js</p>
<pre><code>module.exports.port=3000;
module.exports.say="World";
</code></pre>
<p>Now we can execute the application using</p>
<pre><code>% node index.js
Express server listening on port 3000
</code></pre>
<p>Navigating to <strong><a href="http://localhost:3000" rel="nofollow">http://localhost:3000</a></strong> we find the service running.</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-5-24-39-am.png?w=474" alt="running the NodeJS service" /></p>
<p>Now with a website ready to go we need a a server to put it on and a domain name.</p>
<h2 id="hosting">Hosting with Digital Ocean</h2>
<hr />
<p>For hosting there’s a plutera of new options out there, ranging from Amazon Web Services to Google Compute. However the one I found easiest to use is <a title="Digital Ocean" href="https://www.digitalocean.com/?refcode=c1c4910901e6" target="_blank">Digital Ocean</a>. If you are a student you can get $100 free from the <a title="Github Student Development Pack" href="https://education.github.com/pack" target="_blank">Github Student Development Pack</a> to use towards <a title="Digital Ocean" href="https://www.digitalocean.com/?refcode=c1c4910901e6" target="_blank">Digital Ocean</a>.</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-07-48-am.png?w=474" alt="$100 Promo Code" /></p>
<p>So create an account, login, and let’s create our first droplet!<br />
For most of the applications we’ll be fine with just a basic $5/month box.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-09-54-am.png?w=474" alt="BasicBox" /><br />
Scrolling farther down the page we find the <strong>Select Image</strong> section, just pick a basic Ubuntu box. We could go Applications and find node, but it makes things a little more complicated (ironically).</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-10-47-am.png?w=474" alt="Select Ubuntu" /></p>
<p>This would be the time to provide your SSH keys which can be found using:</p>
<pre><code>% cat ~/.ssh/id_rsa.pub
ssh-rsa aspdjapsdklasjdlkadj12312o3j1d09jasdad0[1j2i123120131u30j 
</code></pre>
<p>Just copy and paste the printed key.</p>
<p>Now we are ready to deploy the droplet! Click Create Droplet!<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-15-56-am.png?w=474" alt="Now creating the droplet" /></p>
<p>Once DigitalOcean finishes creating the droplet you will be taken to the status page where all of the administration and password recovery will be done.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-03-at-6-16-37-am.png?w=474" alt="Status page" /></p>
<p>Ready to connect to our server and configure it!</p>
<h2 id="setup">Setup of NodeJS and Git</h2>
<hr />
<p>With the machine running in the cloud we can <strong>ssh</strong> into it. Grab the IP address from the Status page above. In my case it was <strong>104.236.53.5</strong>.</p>
<pre><code>% ssh root@104.236.53.5
 The authenticity of host '104.236.53.5 (104.236.53.5)' can't be established. (yes/no) yes
 Warning: Permanently added '104.236.53.5' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 15.04 (GNU/Linux 3.19.0-15-generic x86_64)
 * Documentation:  https://help.ubuntu.com/
</code></pre>
<p>Now we are ready to install NodeJS.</p>
<pre><code>% sudo apt-get update
% sudo apt-get install nodejs -y
% sudo apt-get install npm -y
% sudo apt-get install build-essential -y
% sudo npm install forever --global // We'll need this later .... 
</code></pre>
<p>We can test out our installation of NodeJS by executing</p>
<pre><code>% nodejs
&gt; console.log("HelloWorld");
Hello World
undefined
</code></pre>
<p>Quit by pressing CTRL+C twice.</p>
<p>However npm and other applications will be looking for the <strong>node</strong> executable, and not necessarily nodejs. We’ll have to create a symlink pointing from <strong>node</strong> to nodejs.</p>
<pre><code>% which nodejs
/usr/bin/nodejs
% ln -s /usr/bin/nodejs /usr/bin/node
</code></pre>
<p>No we are going to configure a Remote Git Server. This will allow us to automatically deploy applications, to both Github and the server. By doing this we can easily deploy without having to ssh into the server and have to do any additional configuration.</p>
<p>Installing <strong>git</strong> is straight forward using</p>
<pre><code>sudo apt-get install git -y
</code></pre>
<p>Find out where you are located and let’s configure a git server.</p>
<pre><code>% pwd
/root
% ls -l 
total 0

% mkdir -p git/simple.git
% mkdir -p node/simple
% mkdir node/config

% cd git/simple
% git init --bare
</code></pre>
<p>Use your favorite editor to create the post-receive file in the hooks directory.</p>
<pre><code>% vim hooks/post-receive

#!/bin/sh
forever stop index.js
git --work-tree=/root/node/simple --git-dir=/root/git/simple.git checkout -f
cp /root/node/config/simple_configuration.js /root/node/simple/configuration.js
cd /root/node/simple
npm install
forever start  -o logfile.out -e logerror.out index.js
</code></pre>
<p>:wq //write and quit</p>
<p>make it executable</p>
<pre><code>chmod +x hooks/post-receive
</code></pre>
<p>After any push this script will clone a new copy of the repo and overwrite everything in the <strong>/root/git/simple</strong> directory. It will then copy the configuration.js file from the config directory into the <strong>/root/node/simple</strong> directory, this way we can preserve our production configuration files. Afterwards it’ll cd into the <strong>/root/node/simple</strong> directory and perform an <strong>npm install</strong> to install any dependencies and finally run <strong>forever</strong> with the index.js script.</p>
<p>Before we continue let’s create the production configuration script called simple_configuration.js</p>
<pre><code>% vim /root/node/config/simple_configuration.js

module.exports.port=8000;
module.exports.say="World Wide Web";

:wq  //write and quit
</code></pre>
<h2 id="git">Source control</h2>
<hr />
<p>We’ll be keeping our website on Github, so create an account and a repo called <strong>simple</strong>.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-05-at-10-16-29-am.png?w=474" alt="simple repo. " /></p>
<p>First we need to create a .gitignore file so our configuration.js does not commit. We might be storing some sensitive information, like user accounts and database information.</p>
<pre><code>simple/.gitignore
    configuration.js    
    node_modules/

If you already 'git add'ed files and they are being commited even thought they should be ignored, you can reset the git repo using __git rm -r --cached .__
</code></pre>
<p>Let’s make our initial commit, by going back to the terminal.</p>
<p>simple/</p>
<pre><code>    % git init
    % git remote add origin git@github.com:marek5050/simple.git
    % git add .
    % git commit -m "Initial Commit"
    % git push --set-upstream origin master
</code></pre>
<p>**COOL! ** so we have uploaded something onto Github. But let’s do something better! Let’s upload it to our server too!</p>
<p>edit simple/.git/config and add the following lines</p>
<pre><code>[remote "live"]
    url = ssh://root@104.131.140.201:1024/root/git/simple.git
    fetch = +refs/heads/*:refs/remotes/origin/*
</code></pre>
<p>We should now have 2 remotes, one will be the origin (Github) and the second will be our server. Let’s try pushing again..</p>
<pre><code>% git add .
% git commit -m "Pushing to server" 
% git push 
 ...
remote: express@4.12.4 node_modules/express
 ...
remote: info:    Forever processing file: index.js
To ssh://root@104.131.140.201/root/git/simple.git
9ce91b6..d40a423  master -&gt; master
</code></pre>
<p>Awesome let’s go check the server!!</p>
<pre><code>    % ssh root@104.131.140.201
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
</code></pre>
<p>The server is now running. We can now visit the website in our browser.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-05-at-11-28-54-am.png?w=474" alt="Public server running" /></p>
<p>Now we want the website to be accessible via the default http <strong>port 80</strong>. So that when we direct the browser to <strong><a href="http://104.131.140.201/" rel="nofollow">http://104.131.140.201/</a></strong> we’ll access it. We could just tell NodeJS to serve the page on port 80, but that limits the number of web sites we can host on a single machine. For this we can use a web proxy server such as Nginx or Apache. They are both identically configured, so we’ll go with <strong>Nginx</strong>. But before all of this can happen we’ll need to configure a <strong>domain</strong>. We can buy cheap <strong>.com</strong> or nowadays even <strong>.whatever</strong> for about $10. Part of the student pack is a free domain name with <a title="Github Student Development Pack" href="https://education.github.com/pack" target="_blank">Namecheap</a>.</p>
<h2 id="dns">Acquiring a domain name</h2>
<p>We can buy domain names from a variety of hosting services such as <a title="Hostgator" href="http://secure.hostgator.com/%7Eaffiliat/cgi-bin/affiliates/clickthru.cgi?id=dotcz12" target="_blank">Hostgator</a> and <a title="BlueHost" href="http://www.bluehost.com/track/bestofhosting/" target="_blank">Bluehost</a>. But I’ve been using <a title="Enom" href="https://enom.com" target="_blank">Enom</a> for quite a while so I’ll show you how to that up instead.</p>
<p>First search for an available domain that we can acquire.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-44-56-pm.png?w=474" alt="search simplewebsite enom" /></p>
<p>Select an available Top Level Domain (tld,ie .ninja, .net, .com), for us it would be simplewebsite.rocks and click “add” and “checkout.”</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-47-30-pm.png?w=474" alt="click checkout to purchase domain" /></p>
<p>Once the account is created and domain purchased, go and sign into your <a title="Digital Ocean" href="https://www.digitalocean.com/?refcode=c1c4910901e6" target="_blank">Digital Ocean</a>. On the top menu click <strong>DNS</strong>. Type in the name of the DNS you purchased and in the dropdown select the virtual machine spun up in the previous section.<br />
<img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-00-29-pm.png?w=474" alt="DNS Configuration" /></p>
<p>The next screen will give you a bunch of information, from here we’ll just need the <strong>NS</strong> addresses. Usually they are <strong>ns1.digitalocean.com, ns2.digitalocean.com, ns3.digitalocean.com</strong>.</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-01-35-pm.png?w=474" alt="Detailed information about DNS" /></p>
<p>Go back to <a title="Enom" href="https://enom.com" target="_blank">Enom</a> and once logged in there’s a little box called <strong>Domains</strong> click on <strong>Registered</strong>. Find and select the domain and click on <strong>DNS Server Settings.</strong><br />
Configure the Name Servers to the information we found a few seconds ago. Probably <strong>ns1.digitalocean.com, ns2.digitalocean.com, ns3.digitalocean.com</strong>.</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-1-57-08-pm.png?w=474" alt="DNS Server Settings" /></p>
<p>Click save, you’ll get a message that the propagation usually takes about 24 hours. Yet, from experience it usually takes 15 seconds, so we can go on to configure Nginx.</p>
<h2 id="nginx">Configuring Nginx</h2>
<hr />
<p>Okay I ended up buying a different domain name I want to test out, <strong>crossfit.social.</strong><br />
The installation is straight forward with a simple:</p>
<pre><code>% sudo apt-get install nginx
</code></pre>
<p>Now we need to configure it, the quickest way will be by creating a file crossfit.social.conf.<br />
<code></code></p>
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
<p>Nginx should be already running and will need to be reloaded with</p>
<pre><code>% service nginx reload
</code></pre>
<p>The configuration file tells nginx whenever it receives a request for <strong>crossfit.social</strong> it should redirect it to <strong>localhost:8000</strong>, which is the port our NodeJS server is running on.</p>
<p><img src="http://amodernstory.files.wordpress.com/2015/06/wpid-screen-shot-2015-06-07-at-2-25-57-pm.png?w=474" alt="DNS Configured.. We can access the page" /></p>
<h2 id="conclusion">Conclusion</h2>
<p>With forever configured our server will run almost forever and restart whenever it encounters a problem. The only thing we need to worry about now is, what happens when the virtual machine restarts? We need to create a script in the <strong>/etc/init.d/</strong> directory. The script will automatically run forever start /root/node/simple/index.js upon restart. This way we cover most of our corner cases. I’ll paste the script here in a week or so since school is finally starting again!</p>
		<div class="wpcnt">
			<div class="wpa wpmrec">
				<a class="wpa-about" href="https://wordpress.com/about-these-ads/" rel="nofollow"></a>
				<div class="u">
					<script type='text/javascript'>
					(function(g){g.__ATA.initAd({sectionId:26942, width:300, height:250});})(window);
					</script>
				</div>
			</div>
		</div><div id="jp-post-flair" class="sharedaddy sd-like-enabled sd-sharing-enabled"><div class="sharedaddy sd-sharing-enabled"><div class="robots-nocontent sd-block sd-social sd-social-icon-text sd-sharing"><h3 class="sd-title">Share this:</h3><div class="sd-content"><ul><li class="share-google-plus-1"><a rel="nofollow" data-shared="sharing-google-749" class="share-google-plus-1 sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=google-plus-1" target="_blank" title="Click to share on Google+"><span>Google</span></a></li><li class="share-twitter"><a rel="nofollow" data-shared="sharing-twitter-749" class="share-twitter sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=twitter" target="_blank" title="Click to share on Twitter"><span>Twitter</span></a></li><li class="share-facebook"><a rel="nofollow" data-shared="sharing-facebook-749" class="share-facebook sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=facebook" target="_blank" title="Share on Facebook"><span>Facebook</span></a></li><li class="share-tumblr"><a rel="nofollow" data-shared="" class="share-tumblr sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=tumblr" target="_blank" title="Click to share on Tumblr"><span>Tumblr</span></a></li><li class="share-pinterest"><a rel="nofollow" data-shared="sharing-pinterest-749" class="share-pinterest sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=pinterest" target="_blank" title="Click to share on Pinterest"><span>Pinterest</span></a></li><li class="share-linkedin"><a rel="nofollow" data-shared="sharing-linkedin-749" class="share-linkedin sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=linkedin" target="_blank" title="Click to share on LinkedIn"><span>LinkedIn</span></a></li><li><a href="#" class="sharing-anchor sd-button share-more"><span>More</span></a></li><li class="share-end"></li></ul><div class="sharing-hidden"><div class="inner" style="display: none;"><ul><li class="share-pocket"><a rel="nofollow" data-shared="" class="share-pocket sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=pocket" target="_blank" title="Click to share on Pocket"><span>Pocket</span></a></li><li class="share-reddit"><a rel="nofollow" data-shared="" class="share-reddit sd-button share-icon" href="http://amodernstory.com/2015/06/07/creating-a-website-with-nodejs-nginx-and-digital-ocean/?share=reddit" target="_blank" title="Click to share on Reddit"><span>Reddit</span></a></li><li class="share-end"></li><li class="share-end"></li></ul></div></div></div></div></div><div class='sharedaddy sd-block sd-like jetpack-likes-widget-wrapper jetpack-likes-widget-unloaded' id='like-post-wrapper-70359688-749-56cb30fba33a3' data-src='//widgets.wp.com/likes/#blog_id=70359688&amp;post_id=749&amp;origin=amodernstory.wordpress.com&amp;obj_id=70359688-749-56cb30fba33a3' data-name='like-post-frame-70359688-749-56cb30fba33a3'><h3 class='sd-title'>Like this:</h3><div class='likes-widget-placeholder post-likes-widget-placeholder' style='height:55px'><span class='button'><span>Like</span></span> <span class="loading">Loading...</span></div><span class='sd-text-color'></span><a class='sd-link-color'></a></div></div>	</div><!-- .entry-content -->
	
	<footer class="entry-meta"><span class="tag-links"><a href="http://amodernstory.com/tag/digitalocean/" rel="tag">digitalocean</a><a href="http://amodernstory.com/tag/forever/" rel="tag">forever</a><a href="http://amodernstory.com/tag/nginx/" rel="tag">nginx</a><a href="http://amodernstory.com/tag/nodejs/" rel="tag">nodejs</a><a href="http://amodernstory.com/tag/ubuntu/" rel="tag">ubuntu</a></span></footer></article><!-- #post-## -->
	<nav class="navigation post-navigation" role="navigation">
		<h1 class="screen-reader-text">Post navigation</h1>
		<div class="nav-links">
			<a href="http://amodernstory.com/2015/06/05/one-of-those-great-days-again/" rel="prev"><span class="meta-nav">Previous Post</span>One of those great days again</a><a href="http://amodernstory.com/2015/06/12/basic-javascript-testing-using-mocha-and-istanbul/" rel="next"><span class="meta-nav">Next Post</span>Basic Javascript Testing using Mocha and Istanbul</a>		</div><!-- .nav-links -->
	</nav><!-- .navigation --> 
	

	
	</div><!-- #secondary -->

		</div><!-- #main -->
