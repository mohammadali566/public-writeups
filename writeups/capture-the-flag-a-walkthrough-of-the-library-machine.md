# Capture the Flag: A Walkthrough of the "Library" Machine

<h1>
  
  
  Capture the Flag: A Walkthrough of the "Library" Machine
</h1>

<p>In this article, I will walk you through my methodology for solving the "Library" machine, a boot2root CTF challenge. This machine focuses on essential enumeration and privilege escalation techniques, serving as an excellent exercise for those looking to sharpen their Linux security skills.</p>

<h2>
  
  
  1. Enumeration &amp; Reconnaissance
</h2>

<p>The first step in any penetration test is reconnaissance. I started by scanning the target machine to identify open ports and services using <code>nmap</code>.<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">-sV</span> <span class="nt">-sC</span> &lt;target_ip&gt;

</code></pre>

</div>



<p>The scan results revealed two primary services:</p>

<ul>
<li><p><strong>SSH (Port 22):</strong> OpenSSH 7.2p2 (Ubuntu Linux).</p></li>
<li><p><strong>HTTP (Port 80):</strong> Apache httpd 2.4.18.</p></li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkjsp4x77q0gbo884nqb1.jpg"><img alt=" " height="774" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkjsp4x77q0gbo884nqb1.jpg" width="800" /></a></p>

<p>Browsing the web server, I checked the <code>robots.txt</code> file, which gave me a hint about potential usernames. Following this lead, I utilized <code>hydra</code> to perform a brute-force attack on the SSH service using the <code>rockyou.txt</code> wordlist to identify the password for the discovered user (<code>meliodas</code>).</p>

<p><strong>Command used:</strong><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>hydra <span class="nt">-l</span> meliodas <span class="nt">-P</span> /usr/share/wordlists/rockyou.txt ssh://&lt;target_ip&gt;

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fh7cqm1znvaguxu2yvfyl.jpg"><img alt=" " height="295" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fh7cqm1znvaguxu2yvfyl.jpg" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdoavuqy2m71iy5titmoq.jpg"><img alt=" " height="759" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdoavuqy2m71iy5titmoq.jpg" width="800" /></a></p>

<p>The attack was successful, and I obtained the valid credentials.</p>

<h2>
  
  
  2. Initial Access
</h2>

<p>With the discovered credentials, I established an SSH connection to the machine:<br />
(Username): meliodas<br />
(Password): iloveyou1<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ssh meliodas@&lt;target_ip&gt;

</code></pre>

</div>



<p>Once logged in, I navigated to the home directory and successfully retrieved the first flag from <code>user.txt</code>.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcrz0xmlhbmqri6b4ktqf.jpg"><img alt=" " height="388" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcrz0xmlhbmqri6b4ktqf.jpg" width="799" /></a></p>

<h2>
  
  
  3. Privilege Escalation
</h2>

<p>To gain full control of the system, I needed to escalate my privileges. I ran <code>sudo -l</code> to check the permissions assigned to the current user.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F873b3bxt8l3qk1ar8kg5.jpg"><img alt=" " height="496" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F873b3bxt8l3qk1ar8kg5.jpg" width="800" /></a></p>

<p>The output indicated that the user <code>meliodas</code> could run a specific Python script as <code>root</code> without a password:<br />
<code>(ALL) NOPASSWD: /usr/bin/python /home/meliodas/bak.py</code></p>
<h3>
  
  
  Analyzing the Vulnerability
</h3>

<p>I examined the content of <code>bak.py</code>. It was a simple backup script designed to zip the web directory.</p>
<h3>
  
  
  Exploitation
</h3>

<p>Since I had write access to the script, I decided to remove the original backup script first and replace it with a payload to spawn a bash shell.</p>

<p>First, I removed the original <code>bak.py</code> file:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">rm</span> /home/meliodas/bak.py

</code></pre>

</div>



<p>Next, I created a new <code>bak.py</code> file using <code>nano</code>:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nano /home/meliodas/bak.py

</code></pre>

</div>



<p>Inside the editor, I added the following snippet to spawn a root shell:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight python"><code><span class="kn">import</span> <span class="n">os</span>
<span class="n">os</span><span class="p">.</span><span class="nf">system</span><span class="p">(</span><span class="sh">"</span><span class="s">/bin/bash -i</span><span class="sh">"</span><span class="p">)</span>

</code></pre>

</div>



<p>After saving the file and exiting the editor, I executed the script with <code>sudo</code> privileges:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo</span> /usr/bin/python /home/meliodas/bak.py

</code></pre>

</div>



<p>I immediately obtained a root shell. Navigating to the <code>/root</code> directory, I found and read the <code>root.txt</code> file, successfully completing the challenge.</p>

<h2>
  
  
  4. Conclusion
</h2>

<p>This machine highlights the critical importance of secure configuration management. Specifically, it demonstrates why <strong>Sudoers</strong> rules must be strictly defined and why scripts executable with <code>root</code> privileges should be protected from unauthorized modifications.</p>

<p>I hope this walkthrough proves helpful to those starting their journey in cybersecurity. Keep practicing and always think like an attacker to defend like a pro!</p>

[Read original article on DEV](https://dev.to/mohammadali_30/capture-the-flag-a-walkthrough-of-the-library-machine-48d9)