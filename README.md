---


---

<h1 id="nagios-core-installation-on-ubuntu-server-lts">Nagios Core Installation on Ubuntu Server LTS</h1>
<p><strong>Author:</strong> Zakaria Farhati<br>
<strong>Date:</strong> October 2025</p>
<hr>
<h2 id="nagios-overview-🔎">Nagios Overview 🔎</h2>
<ul>
<li><strong>Nagios Core</strong>: A powerful open-source monitoring solution for servers, network devices, services, and host resources. It provides real-time monitoring, alerting, and reporting.</li>
<li><strong>Objective</strong>: Set up and configure Nagios Core on an Ubuntu LTS server for infrastructure monitoring.</li>
<li><strong>Edition</strong>: Nagios Core (Open Source)</li>
<li><strong>Environment</strong>: Ubuntu Server LTS 24.04 (running on a Linux VM)</li>
</ul>
<hr>
<h2 id="installing-nagios-core-⚙️">Installing Nagios Core ⚙️</h2>
<h3 id="update-system-and-install-prerequisites">Update system and install prerequisites</h3>
<p>Install &amp; deploy Nagios Core based on many resources.<br>
Let’s begin with <a href="https://support.nagios.com/kb/article.php?id=96#Ubuntu">Support Nagios</a>.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> apt update <span class="token operator">&amp;&amp;</span> <span class="token function">sudo</span> apt upgrade -y
<span class="token function">sudo</span> apt <span class="token function">install</span> -y autoconf gcc libc6 <span class="token function">make</span> <span class="token function">wget</span> unzip apache2 php libapache2-mod-php libgd-dev ufw openssl libssl-dev
</code></pre>
<h3 id="download-from-source-and-compiling">Download from source and compiling</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">cd</span> /tmp  
<span class="token function">wget</span> -O nagioscore.tar.gz <span class="token punctuation">$(</span>wget -q -O - https://api.github.com/repos/NagiosEnterprises/nagioscore/releases/latest  <span class="token operator">|</span> <span class="token function">grep</span> <span class="token string">'"browser_download_url":'</span> <span class="token operator">|</span> <span class="token function">grep</span> -o <span class="token string">'https://[^"]*'</span><span class="token punctuation">)</span>  
<span class="token function">tar</span> xzf nagioscore.tar.gz

<span class="token function">cd</span> /tmp/nagios-*  
<span class="token function">sudo</span> ./configure --with-httpd-conf<span class="token operator">=</span>/etc/apache2/sites-enabled  
<span class="token function">sudo</span> <span class="token function">make</span> all
</code></pre>
<h3 id="create-user-and-group">Create User And Group</h3>
<p>This creates the  nagios  user and group. The  www-data  user is also added to the  nagios  group.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> install-groups-users  
<span class="token function">sudo</span> <span class="token function">usermod</span> -a -G nagios www-data
</code></pre>
<h3 id="install-nagios-components">Install Nagios components</h3>
<ul>
<li><strong>Binaries</strong> = binary files, CGIs, and HTML files.</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> <span class="token function">install</span>
</code></pre>
<ul>
<li><strong>Services</strong> = the service or daemon files and also configures them to start on boot.</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> install-daemoninit
</code></pre>
<ul>
<li><strong>Command Mode</strong> = the external command file.</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> install-commandmode
</code></pre>
<ul>
<li><strong>Configuration File</strong>s = the <em>SAMPLE</em> configuration files. These are required as Nagios needs some configuration files to allow it to start.</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> install-config
</code></pre>
<ul>
<li><strong>Apache Configuration Files</strong> = Apache web server configuration files and configures Apache settings.</li>
</ul>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> <span class="token function">make</span> install-webconf  
<span class="token function">sudo</span> a2enmod rewrite  
<span class="token function">sudo</span> a2enmod cgi
</code></pre>
<h3 id="configure-ufw-uncomplicated-firewall">Configure UFW (Uncomplicated Firewall)</h3>
<p>You need to allow port 80 inbound traffic on the local firewall so you can reach the Nagios Core web interface.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> ufw allow Apache  
<span class="token function">sudo</span> ufw reload
</code></pre>
<h3 id="create-nagiosadmin-user-account">Create nagiosadmin User Account</h3>
<p>You’ll need to create an Apache user account to be able to log into Nagios.</p>
<p>The following command will create a user account called nagiosadmin and you will be prompted to provide a password for the account.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
</code></pre>
<h2 id="start--test-nagios-core-👌">Start &amp; Test Nagios Core 👌</h2>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> systemctl restart apache2.service
<span class="token function">sudo</span> systemctl start nagios.service
</code></pre>
<p>Nagios is now running, to confirm this you need to log into the Nagios Web Interface.</p>
<p>Point your web browser to the ip address or FQDN <em>(⚠️ DNS required to use FQDN)</em> of your Nagios Core server, for example:<br>
<a href="http://127.0.0.1/nagios">http://127.0.0.1/nagios</a><br>
<a href="http://localhost/nagios">http://localhost/nagios</a><br>
<a href="http://nagios-srv.local/nagios">http://nagios-srv.local/nagios</a> (example)<br>
You will be prompted for a username and password.</p>
<ul>
<li>The username is  nagiosadmin  (you created it in a previous step)</li>
<li>The password is what you provided earlier.</li>
</ul>
<p>Once you have logged in you are presented with the Nagios interface. Congratulations you have installed Nagios Core. 😄 👍</p>
<blockquote>
<p>Caution:<br>
Currently you have only installed the Nagios Core engine. You’ll<br>
notice some errors under the hosts and services along the lines of:</p>
<p>(No output on stdout) stderr:<br>
execvp(/usr/local/nagios/libexec/check_load, …) failed. errno is 2:<br>
No such file or directory</p>
<p>These errors will be resolved once you install the Nagios Plugins</p>
</blockquote>
<h2 id="install-plugins-🔌">Install Plugins 🔌</h2>
<h3 id="prerequists">Prerequists</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> apt <span class="token function">install</span> -y autoconf gcc libc6 libmcrypt-dev <span class="token function">make</span> libssl-dev <span class="token function">wget</span> <span class="token function">bc</span> <span class="token function">gawk</span> <span class="token function">dc</span> build-essential snmp libnet-snmp-perl gettext
</code></pre>
<p>Download + Compile + Install</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">cd</span> /tmp  
<span class="token function">wget</span> -O nagios-plugins.tar.gz <span class="token punctuation">$(</span>wget -q -O - https://api.github.com/repos/nagios-plugins/nagios-plugins/releases/latest  <span class="token operator">|</span> <span class="token function">grep</span> <span class="token string">'"browser_download_url":'</span> <span class="token operator">|</span> <span class="token function">grep</span> -o <span class="token string">'https://[^"]*'</span><span class="token punctuation">)</span>  
<span class="token function">tar</span> zxf nagios-plugins.tar.gz

<span class="token function">cd</span> nagios-plugins-*/  
<span class="token function">sudo</span> ./configure  
<span class="token function">sudo</span> <span class="token function">make</span>  
<span class="token function">sudo</span> <span class="token function">make</span> <span class="token function">install</span>
</code></pre>
<h3 id="test-plugins">Test Plugins</h3>
<p>Point your web browser to the ip address or FQDN of your Nagios Core server, for example:</p>
<p><a href="http://localhost/nagios">http://localhost/nagios</a><br>
<a href="http://192.168.1.18/nagios">http://192.168.1.18/nagios</a> (example)</p>
<p>Go to a host or service object and <em>“Re-schedule the next check”</em> under the Commands menu. The error you previously saw should now disappear and the correct output will be shown on the screen.</p>
<h3 id="services-commands">Services commands</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">sudo</span> systemctl start nagios.service  
<span class="token function">sudo</span> systemctl stop nagios.service  
<span class="token function">sudo</span> systemctl restart nagios.service  
<span class="token function">sudo</span> systemctl status nagios.service
</code></pre>

