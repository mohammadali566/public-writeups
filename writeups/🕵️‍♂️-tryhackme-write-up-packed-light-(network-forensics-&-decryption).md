# 🕵️‍♂️ TryHackMe Write-up: Packed Light (Network Forensics & Decryption)

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdo6jw5rsedr417edud0p.webp"><img alt=" " height="351" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fdo6jw5rsedr417edud0p.webp" width="799" /></a></p>

<p>📑 Challenge Overview<br />
A short capture from the VERA hotel guest network was logged before the connection dropped. Small packets are suspiciously being sent at regular intervals. Someone is smuggling out sensitive data disguised inside ordinary HTTP traffic.</p>

<p>Objectives:</p>

<p>Analyze the provided traffic.pcapng capture file.<br />
Identify the covert communication channel used for data exfiltration.<br />
Extract and reassemble the exfiltrated keystrokes/data.<br />
Decrypt the recovered data and retrieve the flag.<br />
🛠️ Step 1: Initial Setup &amp; Extraction<br />
Start by extracting the challenge zip file on your Kali Linux environment:</p>

<h1>
  
  
  Unzip the challenge archive
</h1>

<p>unzip packed-light-forensics-1784224937659.zip</p>

<h1>
  
  
  Navigate to the directory and inspect files
</h1>

<p>cd packed-light/<br />
ls -la</p>

<p>Step 2: Traffic Analysis using TShark<br />
Inspect the packet capture (traffic.pcapng) for HTTP requests directed to port 8080. We notice traffic featuring a custom User-Agent (ByteLotusClient) along with a suspicious cookie named hotel_sess_state.</p>

<p>Use tshark to filter the HTTP requests and inspect the cookie values:</p>

<p>tshark -r traffic.pcapng -Y “http.request &amp;&amp; tcp.port == 8080” -T fields -e http.cookie</p>

<p>Observation: The requests contain Base64-encoded strings inside hotel_sess_state=... cookies. These represent exfiltrated character values (keystrokes) sent sequentially.</p>

<p>🐍 Step 3: Python Automation &amp; Decryption Script<br />
To handle packet parsing, deduplication, Base64 decoding, and XOR decryption, we write a Python script using scapy:</p>

<p>nano extract_clean.py</p>

<p>Python Script (extract_clean.py):<br />
import base64<br />
from scapy.all import rdpcap, Raw, TCP</p>

<h1>
  
  
  Load the pcap file
</h1>

<p>packets = rdpcap(“traffic.pcapng”)</p>

<p>extracted = []<br />
for pkt in packets:<br />
if pkt.haslayer(TCP) and pkt[TCP].dport == 8080 and pkt.haslayer(Raw):<br />
payload = bytes(pkt[Raw].load)<br />
if b”hotel_sess_state=” in payload and b”ByteLotusClient” in payload:<br />
try:<br />
cookie = payload.split(b”hotel_sess_state=”)[1].split(b”\r\n”)[0].split(b”;”)[0].decode(“utf-8”)<br />
extracted.append(cookie)<br />
except Exception:<br />
pass</p>

<h1>
  
  
  Decrypt using XOR with key byte ‘H’
</h1>

<p>flag = “”.join(chr(base64.b64decode(c)[0] ^ ord(“H”)) for c in extracted)</p>

<p>print(“\n[+] The Flag:”)<br />
print(flag)</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpi81rannil8dj8nw6md9.webp"><img alt=" " height="425" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpi81rannil8dj8nw6md9.webp" width="800" /></a></p>

<p>🚩 Step 4: Execution &amp; Retrieving the Flag<br />
Run the script in your terminal:</p>

<p>python3 extract_clean.py</p>

<p>[+] The Flag:<br />
THM{V3r4_1s_w4tch1ng_0veR_you}</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Falmx6sj5tioeru4f15md.webp"><img alt=" " height="100" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Falmx6sj5tioeru4f15md.webp" width="254" /></a></p>

<p>🏁 Conclusion<br />
Final Flag: THM{V3r4_1s_w4tch1ng_0veR_you}</p>

<p>Key Takeaways:</p>

<p>Data exfiltration techniques often hide within benign-looking HTTP headers and Cookie fields.<br />
Analyzing request signatures (like custom User-Agents) quickly isolates suspicious packets.<br />
Scripting with Scapy allows seamless parsing and automated decryption of custom network traffic.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fo2b87px9tzrvhcmm07sb.webp"><img alt=" " height="365" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fo2b87px9tzrvhcmm07sb.webp" width="800" /></a></p>

[Read original article on DEV](https://dev.to/mohammadali_30/tryhackme-write-up-packed-light-network-forensics-decryption-ndb)