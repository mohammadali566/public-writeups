# Write-up: TryHackMe Room “Anonymous”

<p>Welcome to this comprehensive technical article where we will walk through the step-by-step process of solving the popular Anonymous room on TryHackMe. This room serves as a practical exercise to enhance ethical hacking skills and focus on offensive security concepts in an organized manner.</p>

<ol>
<li>Reconnaissance
The journey always begins with gathering information about the target using standard enumeration tools. We performed a comprehensive scan using Nmap to identify open ports and running services on the target machine:</li>
</ol>

<p>nmap -sC -sV <br />
The scan results revealed several key ports and services:</p>

<p>Port 21 (FTP): vsftpd service allowing anonymous login (Anonymous FTP login allowed), along with a writable directory named scripts.<br />
Port 22 (SSH): Secure shell service OpenSSH.<br />
Ports 139 / 445 (SMB): Samba file sharing protocol.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhyo0kxlxcbpzngwcgfvn.webp"><img alt=" " height="333" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhyo0kxlxcbpzngwcgfvn.webp" width="800" /></a></p>

<ol>
<li>FTP Exploitation and Anonymous Login
Since the FTP server allowed access via the anonymous account, we logged in directly without a password:</li>
</ol>

<p>ftp </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4fprj2qq1z3c9ir9l6m3.webp"><img alt=" " height="77" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4fprj2qq1z3c9ir9l6m3.webp" width="799" /></a></p>

<p>Exploring the directories led us to the scripts folder, which contained the following files:<br />
clean.sh<br />
removed_files.log<br />
to_do.txt<br />
We noticed that the clean.sh script runs periodically (via a Cron Job), which creates an ideal opportunity for command injection or Cron Job Hijacking.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkhi9gbnxszu7ars9iwqv.webp"><img alt=" " height="80" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fkhi9gbnxszu7ars9iwqv.webp" width="800" /></a></p>

<p>Before moving to initial access, we downloaded the original clean.sh file from the server to our local machine to analyze and edit it using the following command:</p>

<p>ftp&gt; get clean.sh</p>

<ol>
<li>Initial Access
We edited the local clean.sh file using nano to add our reverse shell payload:</li>
</ol>

<p>nano clean.sh</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frjkmqigdw3isgz8p7n5t.webp"><img alt=" " height="52" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frjkmqigdw3isgz8p7n5t.webp" width="324" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdgj4ijaeyyej14nkrf4t.webp"><img alt=" " height="333" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdgj4ijaeyyej14nkrf4t.webp" width="800" /></a></p>

<p>After modifying the script, we uploaded the updated file back to the scripts directory via FTP:</p>

<p>ftp&gt; put clean.sh</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd0ol498lxn8kxwvflvgz.webp"><img alt=" " height="125" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd0ol498lxn8kxwvflvgz.webp" width="799" /></a></p>

<p>Simultaneously, we set up a Netcat listener on our Kali machine:</p>

<p>nc -lvnp 4444</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftl1eq1m0t1wgm0i3a602.webp"><img alt=" " height="144" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftl1eq1m0t1wgm0i3a602.webp" width="800" /></a></p>

<p>Once the script executed automatically on the target system, we successfully received the connection and obtained a shell as the regular user namelessone. From there, we were able to read the first user flag:</p>

<p>User Flag: ………………………………………….</p>

<ol>
<li>Privilege Escalation to Root
To search for potential vectors to escalate our privileges to root, we looked for files with the SUID permission bit set using the following command:</li>
</ol>

<p>find / -perm -4000 -type f 2&gt;/dev/null</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2krtiw5wtabaeihk7jik.webp"><img alt=" " height="553" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2krtiw5wtabaeihk7jik.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fh0edzxfty5gqk3luxgnv.webp"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fh0edzxfty5gqk3luxgnv.webp" width="799" /></a></p>

<p>The results highlighted an unexpected binary — specifically, the use of /usr/bin/env combined with the shell (/bin/sh -p). Following the guidance from the well-known GTFOBins platform for privilege escalation:</p>

<p>/usr/bin/env /bin/sh -p<br />
Executing this command instantly dropped us into a root shell! We then navigated to the root directory to read the final flag:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Forjqc4jc4moltd0tluzn.webp"><img alt=" " height="72" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Forjqc4jc4moltd0tluzn.webp" width="421" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftpafw0mfzmdm7w2qqqaa.webp"><img alt=" " height="472" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ftpafw0mfzmdm7w2qqqaa.webp" width="468" /></a></p>

<p>Root Flag: …………………………………………</p>

[Read original article on DEV](https://dev.to/mohammadali_30/write-up-tryhackme-room-anonymous-2541)