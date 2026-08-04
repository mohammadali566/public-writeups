# From Insecure Deserialization to Root: TryHackMe “Beach Bar” Walkthrough

<p>Introduction<br />
In this write-up, we will walk through the Beach Bar room on TryHackMe. We will cover how we accessed the web application using default credentials, how directory enumeration revealed hidden web endpoints, how we navigated directly to the import page, how an insecure YAML deserialization vulnerability allowed us to gain a reverse shell, how we located and read the user flag, and how process enumeration exposed cleartext credentials leading to a full root escalation.</p>

<p>Phase 1: Accessing the Web Application &amp; Reconnaissance</p>

<ol>
<li>Logging into the DJ Booth
Upon navigating to the target web application, we are greeted with the DJ booth sign-in page. We authenticate using the default credentials for the room:</li>
</ol>

<p>Username: DJ<br />
Password: DJ</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhqp2p4lj1z0up4s8swtx.webp"><img alt=" " height="322" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhqp2p4lj1z0up4s8swtx.webp" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frkvoglnvwg4z9mzplkbo.webp"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frkvoglnvwg4z9mzplkbo.webp" width="800" /></a></p>

<ol>
<li>Reconnaissance &amp; Nmap Scan
We also run an Nmap scan against the target IP to discover open ports and services:</li>
</ol>

<p>nmap -sV -sC <br />
The scan reveals two main open ports:</p>

<p>Port 22/tcp (SSH): Running OpenSSH 9.6p1.<br />
Port 80/tcp (HTTP): Running a web application powered by Gunicorn.<br />
Press enter or click to view image in full size</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fo19kxpxsppetgup80v9y.webp"><img alt=" " height="313" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fo19kxpxsppetgup80v9y.webp" width="800" /></a></p>

<p>Directory Fuzzing with Gobuster<br />
To discover hidden directories and endpoints on the web server, we run gobuster using the medium directory wordlist:</p>

<p>gobuster dir -u http:// -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt<br />
As seen in our directory enumeration results:</p>

<p>/login (Status: 200)<br />
/logout (Status: 302)<br />
/export (Status: 302)<br />
/dashboard (Status: 302)<br />
/import (Status: 302)</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3w5i4zjl0u6dwnbxyaia.webp"><img alt=" " height="287" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3w5i4zjl0u6dwnbxyaia.webp" width="800" /></a></p>

<p>Navigating to the Import Page<br />
After running Gobuster and discovering the available endpoints, we append /import to the target URL (http:///import) in our browser to access the playlist management and import features.</p>

<p>Phase 2: Exploiting Insecure Deserialization (RCE)<br />
The application features an Import function at this endpoint that processes uploaded playlist YAML files. Reviewing the backend code reveals an insecure deserialization flaw using yaml.load() without a safe loader, which allows Remote Code Execution (RCE).</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwwxp08l4lhz6fbtm7j4z.webp"><img alt=" " height="694" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwwxp08l4lhz6fbtm7j4z.webp" width="800" /></a></p>

<ol>
<li>Setting Up a Netcat Listener
Open a Netcat listener on your Kali terminal to catch the reverse shell:</li>
</ol>

<p>nc -lvnp 4444</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fn84cdo9m6jribcylk9ki.webp"><img alt=" " height="659" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fn84cdo9m6jribcylk9ki.webp" width="800" /></a></p>

<ol>
<li>Crafting and Submitting the Payload
Instead of file upload scripts, we can target the input form directly. Copy the following payload into the Playlist YAML field on the web interface:</li>
</ol>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbvwaqy2pbkunv8umcbek.webp"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbvwaqy2pbkunv8umcbek.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyt75i39pykrzth61d0dd.webp"><img alt=" " height="227" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fyt75i39pykrzth61d0dd.webp" width="714" /></a></p>

<p>(Make sure to change 10.14.X.X to your Kali tun0 IP address, and 4444 to your open listener port).</p>

<ol>
<li>Triggering the Exploit
Click on Load playlist. This executes the payload through the application’s vulnerable YAML parser, granting us an initial foothold as the low-privilege user bartender.</li>
</ol>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5vm4ltkmxf8d2n020lu9.webp"><img alt=" " height="662" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F5vm4ltkmxf8d2n020lu9.webp" width="800" /></a><br />
Phase 3: Finding the User Flag<br />
Once we obtain our initial reverse shell connection, we can navigate through the file system to locate the user flag.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwgxwtbzljbzdezbkpvyl.webp"><img alt=" " height="199" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwgxwtbzljbzdezbkpvyl.webp" width="799" /></a></p>

<p>Navigate to the home directory of the bartender<br />
cd /home/bartender<br />
List the directory contents to find user.txt:</p>

<p>ls<br />
Read the contents of the flag file:</p>

<p>cat user.txt</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxl1elosug2v26zq9xkmq.webp"><img alt=" " height="250" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxl1elosug2v26zq9xkmq.webp" width="800" /></a></p>

<p>User Flag Found: THM{................................}</p>

<p>Phase 4: Privilege Escalation via Process Enumeration<br />
Once inside the system as bartender, we look for running services to find potential privilege escalation vectors. Running ps aux | grep python reveals the active background processes:</p>

<p>ps aux | grep python<br />
As shown in our process inspection:</p>

<p>The jukeboxd.py script runs directly under the root user.<br />
It accidentally passes the stream password in cleartext via command-line arguments: --stream-pass SunsetSpritz2024!.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj86rmw39z8y5d3lj44q4.webp"><img alt=" " height="256" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj86rmw39z8y5d3lj44q4.webp" width="800" /></a></p>

<p>Phase 5: Gaining Root Access &amp; Finding the Root Flag<br />
Switching to Root: With the exposed cleartext password (SunsetSpritz2024!), we leverage credential reuse to switch user contexts directly to the administrator:<br />
su - root<br />
Enter the password when prompted.<br />
password : SunsetSpritz2024!<br />
Locating and Reading the Root Flag:<br />
Navigate to the root directory and list its contents:<br />
cd /root<br />
ls<br />
Read the contents of root.txt:<br />
cat root.txt</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6i530snpb8rw54b3rv2o.webp"><img alt=" " height="106" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6i530snpb8rw54b3rv2o.webp" width="797" /></a></p>

<p>Root Flag Found: THM{..................................}</p>

[Read original article on DEV](https://dev.to/mohammadali_30/from-insecure-deserialization-to-root-tryhackme-beach-bar-walkthrough-hpk)