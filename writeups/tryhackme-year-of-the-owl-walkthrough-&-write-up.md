# TryHackMe: Year of the Owl Walkthrough & Write-up

<p>Introduction<br />
"Year of the Owl" is an intermediate-level Windows machine available on TryHackMe. It presents a realistic pentesting scenario starting from reconnaissance, exploiting SNMP enumeration, password spraying via WinRM/SMB, recovering SAM and SYSTEM backup hives from the Recycle Bin, cracking password hashes with Impacket, and finally escalating privileges to Administrator.</p>

<p>In this write-up, we will walk through the complete enumeration and exploitation chain step-by-step.</p>




<h2>
  
  
  Phase 1: Reconnaissance &amp; Port Scanning
</h2>

<p>We start by running an initial Nmap scan to identify open ports and services on the target machine (<code>10.67.173.83</code>).</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6nhfomfxhwkah49ooyag.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6nhfomfxhwkah49ooyag.png" width="800" /></a></p>

<p>Further UDP SNMP enumeration reveals port <code>161/udp</code> open/filtered. We can use Metasploit to enumerate users through SNMP.</p>

<p>Using the community string <code>openview</code>, the SNMP enumeration successfully discovers 5 user accounts on the system:</p>

<ul>
<li><code>Administrator</code></li>
<li><code>DefaultAccount</code></li>
<li><code>Guest</code></li>
<li><code>Jareth</code></li>
<li><code>WDAGUtilityAccount</code></li>
</ul>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fx4s4zas3b73b3frpm2uw.png"><img alt=" " height="427" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fx4s4zas3b73b3frpm2uw.png" width="800" /></a></p>




<h2>
  
  
  Phase 2: Credential Stuffing &amp; Password Spraying
</h2>

<p>With the discovered username <code>jareth</code>, we proceed to perform credential stuffing and password spraying against SMB (<code>445</code>) and WinRM (<code>5985</code>) using <code>crackmapexec</code> combined with the standard <code>rockyou.txt</code> wordlist.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz7iafzwhrslavspa83a9.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz7iafzwhrslavspa83a9.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F81u2r8eyy4snmxj04e64.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F81u2r8eyy4snmxj04e64.png" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzseulolsn3fui76o1ivx.png"><img alt=" " height="425" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzseulolsn3fui76o1ivx.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbfwy9m0y3u794ztcio1f.png"><img alt=" " height="424" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbfwy9m0y3u794ztcio1f.png" width="799" /></a></p>

<p>After iterating through the wordlist, a valid match is found for user <code>jareth</code>:</p>

<ul>
<li>
<strong>Password Found:</strong> <code>sarah</code> (marked as <code>Pwn3d!</code>)</li>
</ul>




<h2>
  
  
  Phase 3: Initial Access via WinRM
</h2>

<p>Now that we have valid credentials (<code>jareth:sarah</code>), we can establish an interactive remote shell using <code>evil-winrm</code>.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmg7usuqqkdcuhtlvzaw1.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmg7usuqqkdcuhtlvzaw1.png" width="799" /></a></p>

<p>Once connected, we navigate to Jareth's desktop and retrieve the user flag (<code>user.txt</code>):</p>

<ul>
<li>
<strong>User Flag:</strong> <code>THM{Y2I0NDJjODY2YmI2Y2U4M2IwZT81}</code>
</li>
</ul>




<h2>
  
  
  Phase 4: Privilege Escalation &amp; Hive Recovery
</h2>

<p>To escalate our privileges, we inspect system privileges and search for sensitive files or backups. While investigating the file system, we locate registry backup hives stored inside the Recycle Bin (<code>$Recycle.bin</code>).</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgm2mgm7p80wyhc84yr6x.png"><img alt=" " height="425" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgm2mgm7p80wyhc84yr6x.png" width="800" /></a></p>

<p>We locate <code>sam.bak</code> and <code>system.bak</code> inside the Recycle Bin directory for the user SID. We copy these files locally to Jareth's directory and download them to our attacking machine using the built-in <code>evil-winrm</code> download command.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgykv43v4o6ma7rky5xvg.png"><img alt=" " height="425" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgykv43v4o6ma7rky5xvg.png" width="800" /></a></p>

<p>Back on our Kali Linux attacker machine, we use <code>impacket-secretsdump</code> to extract the NTLM password hashes from the offline SAM and SYSTEM backup hives.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F21d3ql5ngfhtmjtk05jb.png"><img alt=" " height="424" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F21d3ql5ngfhtmjtk05jb.png" width="800" /></a></p>

<p>The hash dump successfully reveals the NTLM password hashes for all local accounts, including the <code>Administrator</code>:</p>

<ul>
<li>
<strong>Administrator Hash:</strong> <code>aad3b435b51404eeaad3b435b51404e:6bc99e9edcfecf9662fb0c0ddcf7a7a</code>
</li>
</ul>




<h2>
  
  
  Phase 5: Gaining Administrator Access &amp; Root Flag
</h2>

<p>Armed with the Administrator hash, we perform a Pass-the-Hash (PtH) attack via <code>evil-winrm</code> to connect directly as the <code>Administrator</code>.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg7z0pehhbkp0d7msvv4m.png"><img alt=" " height="426" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg7z0pehhbkp0d7msvv4m.png" width="800" /></a></p>

<p>Finally, we navigate to the Administrator's desktop, read <code>admin.txt</code>, and capture the final root flag:</p>

<ul>
<li>
<strong>Root Flag:</strong> <code>THM{YWFiZTM1MjFjMzRiODgyY1wyUwyZlZWM2}</code>
</li>
</ul>




<p>Conclusion<br />
"Year of the Owl" is an exceptionally well-designed lab that highlights the risks of:</p>

<ol>
<li>Information leakage via SNMP.</li>
<li>Weak password policies and password spraying vulnerabilities over WinRM/SMB.</li>
<li>Leaving sensitive backup files (like SAM and SYSTEM hives) inside the Recycle Bin.</li>
</ol>

[Read original article on DEV](https://dev.to/mohammadali_30/tryhackme-year-of-the-owl-walkthrough-write-up-5dgf)