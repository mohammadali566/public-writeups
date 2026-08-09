# Management Wants a Word - TryHackMe Write-up (Hacker Holidays Day 14)

<p>In this write-up, we will walk through the digital forensics and incident response (DFIR) steps to solve the <strong>Management Wants a Word</strong> challenge from TryHackMe's Hacker Holidays. </p>




<h2>
  
  
  1. Initial Setup and Reconnaissance
</h2>

<p>First, we navigate to the challenge directory and inspect the files extracted via KAPE:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cd </span>management-wants-a-word-forensics-hh-day-14
<span class="nb">cd </span>KAPE

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F01cqwhrlr1bzvxs2ct4b.png"><img alt=" " height="46" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F01cqwhrlr1bzvxs2ct4b.png" width="451" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frinm05k85mxo01ri3n6a.png"><img alt=" " height="69" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Frinm05k85mxo01ri3n6a.png" width="616" /></a></p>

<p>Identify the suspicious backup file type and inspect its header:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>file C/Users/vera/Documents/backup
xxd <span class="nt">-l</span> 64 C/Users/vera/Documents/backup

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fagg0wxto7kgwr34gayfp.png"><img alt=" " height="59" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fagg0wxto7kgwr34gayfp.png" width="659" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqxjqskclutqirirysdd7.png"><img alt=" " height="107" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqxjqskclutqirirysdd7.png" width="639" /></a></p>

<p>Search for sensitive Google Chrome browser artifacts belonging to the user <code>vera</code>:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>find C/Users/vera <span class="nt">-type</span> f <span class="se">\(</span> <span class="nt">-iname</span> <span class="s2">"Login Data"</span> <span class="nt">-o</span> <span class="nt">-iname</span> <span class="s2">"Local State"</span> <span class="nt">-o</span> <span class="nt">-iname</span> <span class="s2">"Web Data"</span> <span class="nt">-o</span> <span class="nt">-iname</span> <span class="s2">"History"</span> <span class="se">\)</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F48r5hyjux5z4x8oz1yba.png"><img alt=" " height="112" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F48r5hyjux5z4x8oz1yba.png" width="800" /></a></p>




<h2>
  
  
  2. Extracting Windows Secrets &amp; DPAPI Masterkey
</h2>

<p>Extract the Windows registry hives to retrieve system security data:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>impacket-secretsdump <span class="nt">-sam</span> C/Windows/System32/config/SAM <span class="nt">-system</span> C/Windows/System32/config/SYSTEM <span class="nt">-security</span> C/Windows/System32/config/SECURITY LOCAL

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmjrrqnnw1kxr7wvstbrk.png"><img alt=" " height="303" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmjrrqnnw1kxr7wvstbrk.png" width="800" /></a></p>

<p>Decrypt the user's DPAPI Masterkey using the account password (<code>minivera</code>):<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>impacket-dpapi masterkey <span class="nt">-file</span> <span class="s1">'C/Users/vera/AppData/Roaming/Microsoft/Protect/S-1-5-21-2529683458-431225740-1723070931-1000/c90719ef-5b98-474e-b934-136d606a702a'</span> <span class="nt">-sid</span> <span class="s1">'S-1-5-21-2529683458-431225740-1723070931-1000'</span> <span class="nt">-password</span> <span class="s1">'minivera'</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F86q7empd9mhtfhfh5rql.png"><img alt=" " height="221" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F86q7empd9mhtfhfh5rql.png" width="800" /></a></p>

<p>Store the resulting masterkey value in a shell variable:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nv">MASTERKEY</span><span class="o">=</span><span class="s1">'5e5715ec9b6df5a86e97902692a66d28e691f05d5bc1e04d0159cfe960e94c978c07e5004a0179d3a96df2468885a28175b0b02cc064445f116a752d2b3e9d40'</span>

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ffiul958o18doc5k8m7zs.png"><img alt=" " height="52" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ffiul958o18doc5k8m7zs.png" width="798" /></a></p>




<h2>
  
  
  3. Decrypting the Chrome AES Key
</h2>

<p>Locate the <code>Local State</code> file path and extract the encrypted key:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nv">LOCAL_STATE</span><span class="o">=</span><span class="s2">"</span><span class="si">$(</span>find <span class="s2">"</span><span class="nv">$PWD</span><span class="s2">/C/Users/vera"</span> <span class="nt">-type</span> f <span class="nt">-iname</span> <span class="s1">'Local State'</span> <span class="nt">-print</span> <span class="nt">-quit</span><span class="si">)</span><span class="s2">"</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fe8o2jx7cdpfdahmhr90y.png"><img alt=" " height="67" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fe8o2jx7cdpfdahmhr90y.png" width="648" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">echo</span> <span class="nv">$LOCAL_STATE</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ft5ye3co5d3jr41ecif60.png"><img alt=" " height="63" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Ft5ye3co5d3jr41ecif60.png" width="800" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">printf</span> <span class="s1">'%s\n'</span> <span class="s2">"</span><span class="nv">$LOCAL_STATE</span><span class="s2">"</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7ou9frq0dqw8fqex4a20.png"><img alt=" " height="61" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F7ou9frq0dqw8fqex4a20.png" width="799" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>jq <span class="nt">-r</span> <span class="s1">'.os_crypt.encrypted_key'</span> <span class="s2">"</span><span class="nv">$LOCAL_STATE</span><span class="s2">"</span> | <span class="nb">base64</span> <span class="nt">-d</span> | xxd
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6sufhej761o52rdbos4g.png"><img alt=" " height="377" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F6sufhej761o52rdbos4g.png" width="704" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>jq <span class="nt">-r</span> <span class="s1">'.os_crypt.encrypted_key'</span> <span class="s2">"</span><span class="nv">$LOCAL_STATE</span><span class="s2">"</span> | <span class="nb">base64</span> <span class="nt">-d</span> | <span class="nb">tail</span> <span class="nt">-c</span> +6 <span class="o">&gt;</span> chrome-key.dpapi
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpkr1qd5gauyjxnp86fcl.png"><img alt=" " height="81" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpkr1qd5gauyjxnp86fcl.png" width="650" /></a></p>

<p>Run a Python script to decrypt the Chrome key using the Masterkey:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 - <span class="s2">"</span><span class="nv">$MASTERKEY</span><span class="s2">"</span> <span class="o">&lt;&lt;</span><span class="sh">'</span><span class="no">PY</span><span class="sh">'
import sys
from impacket.dpapi import DPAPI_BLOB
masterkey = bytes.fromhex(sys.argv[1])
with open("chrome-key.dpapi", "rb") as f:
    blob = DPAPI_BLOB(f.read())
decrypted = blob.decrypt(masterkey)
if decrypted is None:
    raise SystemExit("DPAPI decryption failed")
with open("chrome-aes.key", "wb") as f:
    f.write(decrypted)
print(f"Wrote {len(decrypted)} bytes")
print(f"Chrome AES key: {decrypted.hex()}")
</span><span class="no">PY
</span></code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3ggb6k90zzlk4zjk1r8n.png"><img alt=" " height="317" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3ggb6k90zzlk4zjk1r8n.png" width="800" /></a></p>

<p>..................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">wc</span> <span class="nt">-c</span> chrome-aes.key

</code></pre>

</div>



<p>................<br />
<a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgwui8cs17szyzehetsay.png"><img alt=" " height="58" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgwui8cs17szyzehetsay.png" width="688" /></a><br />
............</p>



<ol>
<li>Extracting Saved Credentials</li>
</ol>

<p>Find the <code>Login Data</code> SQLite database and run a script to decrypt stored passwords using AES-GCM:<br />
................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nv">LOGIN_DATA</span><span class="o">=</span><span class="s2">"</span><span class="si">$(</span>find <span class="s2">"</span><span class="nv">$PWD</span><span class="s2">/C/Users/vera"</span> <span class="nt">-type</span> f <span class="nt">-iname</span> <span class="s1">'Login Data'</span> <span class="nt">-print</span> <span class="nt">-quit</span><span class="si">)</span><span class="s2">"</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fswkic3z4jsjrluljz479.png"><img alt=" " height="57" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fswkic3z4jsjrluljz479.png" width="712" /></a><br />
.................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">printf</span> <span class="s1">'%s\n'</span> <span class="s2">"</span><span class="nv">$LOGIN_DATA</span><span class="s2">"</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmg3uhfqw07r3vnxjnp72.png"><img alt=" " height="71" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fmg3uhfqw07r3vnxjnp72.png" width="800" /></a><br />
.............<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>file <span class="s2">"</span><span class="nv">$LOGIN_DATA</span><span class="s2">"</span>
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqo15hzoh4zc54uz7kyzk.png"><img alt=" " height="81" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fqo15hzoh4zc54uz7kyzk.png" width="800" /></a><br />
...........<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>python3 - <span class="s2">"</span><span class="nv">$LOGIN_DATA</span><span class="s2">"</span> ./chrome-aes.key <span class="o">&lt;&lt;</span><span class="sh">'</span><span class="no">PY</span><span class="sh">'
import sqlite3
import sys
from pathlib import Path
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
database = Path(sys.argv[1]).resolve()
keyfile = Path(sys.argv[2]).resolve()
if not database.is_file():
    raise SystemExit(f"Missing database: {database}")
key = keyfile.read_bytes()
if len(key) != 32:
    raise SystemExit(f"Unexpected AES key length: {len(key)}")
db = sqlite3.connect(database.as_uri() + "?mode=ro", uri=True)
for url, username, encrypted in db.execute("""
    SELECT origin_url, username_value, password_value
    FROM logins
"""):
    if not encrypted:
        continue
    try:
        iv = encrypted
        payload = encrypted[15:]
        aesgcm = AESGCM(key)
        decrypted = aesgcm.decrypt(iv, payload, None)
        password = decrypted.decode('utf-8')
    except Exception as e:
        password = f"[Decryption Failed: {e}]"
    print(f"URL: {url}")
    print(f"Username: {username}")
    print(f"Password: {password}")
    print("-" * 40)
</span><span class="no">PY
</span></code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjdkeiqas9ng34ui27a2s.png"><img alt=" " height="583" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjdkeiqas9ng34ui27a2s.png" width="800" /></a><br />
..........</p>



<ol>
<li>Mounting the VeraCrypt Container</li>
</ol>

<p>Open the VeraCrypt encrypted backup container, create a mount point, and mount it in read-only mode:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>cryptsetup tcryptOpen <span class="se">\ </span><span class="nt">--veracrypt</span> <span class="se">\ </span><span class="s1">'C/Users/vera/Documents/backup'</span> <span class="se">\ </span>vera_backup
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjjj82qk62ghupmn0oes5.png"><img alt=" " height="102" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjjj82qk62ghupmn0oes5.png" width="655" /></a><br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>password : Wh4t1sV3raD0ing0nTh1sH0st
</code></pre>

</div>



<p>.........<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo mkdir</span> <span class="nt">-p</span> /mnt/vera
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2rp2cmk8pvz1ggphknn1.png"><img alt=" " height="50" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2rp2cmk8pvz1ggphknn1.png" width="674" /></a><br />
.............<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>mount <span class="nt">-o</span> ro /dev/mapper/vera_backup /mnt/vera
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuy91m2u9dsn6l0o2u0s8.png"><img alt=" " height="42" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fuy91m2u9dsn6l0o2u0s8.png" width="668" /></a><br />
...............</p>

<p>List the container contents and navigate to the financial documents:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">ls</span> /mnt/vera
<span class="nb">ls</span> /mnt/vera/secret_financial_documents

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9kc4mi8ytza2ggp7p435.png"><img alt=" " height="120" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9kc4mi8ytza2ggp7p435.png" width="669" /></a></p>

<p>..................<br />
Open the target invoice PDF file to retrieve the flag:<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code>xdg-open /mnt/vera/secret_financial_documents/important_invoice_byte_lotus.pdf

</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz8xsju37mfumg30jlbi8.png"><img alt=" " height="48" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fz8xsju37mfumg30jlbi8.png" width="698" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhkcvl2gu1ycjc9m84ys4.png"><img alt=" " height="651" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fhkcvl2gu1ycjc9m84ys4.png" width="568" /></a></p>

<p>Flag:<br />
THM{--<em>---</em>----_---_AL0ng?!}</p>


<h2>
  
  
  6. Cleanup
</h2>

<p>Safely unmount the filesystem and close the encrypted device:<br />
..................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">cd</span> ~
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fycan6vkdsdyy327zcsm4.png"><img alt=" " height="55" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fycan6vkdsdyy327zcsm4.png" width="653" /></a><br />
................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>fuser <span class="nt">-km</span> /mnt/vera
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9kcye802nur44n70922l.png"><img alt=" " height="66" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F9kcye802nur44n70922l.png" width="283" /></a><br />
........................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>umount /mnt/vera
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjt7uu8os0yxvbt8u01yh.png"><img alt=" " height="47" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fjt7uu8os0yxvbt8u01yh.png" width="310" /></a><br />
.........................<br />
</p>

<div class="highlight js-code-highlight">
<pre class="highlight shell"><code><span class="nb">sudo </span>cryptsetup close vera_backup
</code></pre>

</div>



<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg22kwx2vc99kf6yu6nec.png"><img alt=" " height="47" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fg22kwx2vc99kf6yu6nec.png" width="381" /></a></p>

<h2>
  
  
  .........................
</h2>

[Read original article on DEV](https://dev.to/mohammadali_30/management-wants-a-word-tryhackme-write-up-hacker-holidays-day-14-4bkp)