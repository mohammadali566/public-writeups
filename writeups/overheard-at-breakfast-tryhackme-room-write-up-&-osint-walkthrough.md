# Overheard at Breakfast: TryHackMe Room Write-up & OSINT Walkthrough

<p>Two strangers. One conversation. One profile they never meant to reveal.</p>

<p>Open Source Intelligence (OSINT) is one of the most exciting and dynamic fields in cybersecurity. It demonstrates how seemingly innocuous snippets of information shared online can expose a complete digital profile. In this write-up, we explore the TryHackMe room “Overheard at Breakfast”, breaking down how a casual conversation between two resort guests at “Byte Lotus” leads to uncovering a hidden identity and capturing the flag.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgqdy2pv2d1jqmkc2fc22.webp"><img alt=" " height="206" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fgqdy2pv2d1jqmkc2fc22.webp" width="798" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp4ckze57v1y4ake7enws.webp"><img alt=" " height="141" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp4ckze57v1y4ake7enws.webp" width="800" /></a></p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp93jd9mfz0qjqa7s4gql.webp"><img alt=" " height="79" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fp93jd9mfz0qjqa7s4gql.webp" width="539" /></a></p>

<ol>
<li>The Scenario &amp; Reconnaissance
The room drops us into a simulated chat transcript between two users: Ponzi — Influencer and Lambo!. They are chatting late at night about their stay at the Byte Lotus resort:</li>
</ol>

<p>Key Conversation Excerpt:</p>

<p>Lambo!: “Absolutely, Byte Lotus is treating me nice, love the food, weather and overall vibes. Will probably come back next year too.”</p>

<p>Lambo! (regarding social media): “Yeah nowadays I don’t really use much social media… Though I’m still out there, I used to use this free tool that let me upload my profile and link other media accounts was neat, until I wiped everything. Started with a G if I remember correctly.”</p>

<p>Contact Email: <a href="mailto:lambobytelotushotel@gmail.com">lambobytelotushotel@gmail.com</a></p>

<p>From this conversation, we extract critical intelligence:</p>

<p>The target’s handle/name is Lambo.<br />
They used a profile-linking tool starting with “G” where they previously uploaded a profile picture.<br />
Their email address is explicitly provided: <a href="mailto:lambobytelotushotel@gmail.com">lambobytelotushotel@gmail.com</a>.</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpicmywygcj4ylo97iy9n.webp"><img alt=" " height="521" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fpicmywygcj4ylo97iy9n.webp" width="800" /></a></p>

<ol>
<li>Connecting the Clues: Gravatar OSINT
The clue pointing towards a free tool starting with “G” used for hosting avatars and linking profiles immediately directs our attention to Gravatar (Globally Recognized Avatar), a service tied directly to email addresses using cryptographic hashing.</li>
</ol>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2wsjvumqg1jh50i5khi7.webp"><img alt=" " height="343" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F2wsjvumqg1jh50i5khi7.webp" width="800" /></a></p>

<p>By checking the target email address on Gravatar (<a href="https://gravatar.com/site/check" rel="noopener noreferrer">https://gravatar.com/site/check</a>), we uncover the associated profile URL and avatar hash:</p>

<p>Email: <a href="mailto:lambobytelotushotel@gmail.com">lambobytelotushotel@gmail.com</a><br />
Profile URL: <a href="https://gravatar.com/d43faaf69d71056793bd037b8d6e321acad" rel="noopener noreferrer">https://gravatar.com/d43faaf69d71056793bd037b8d6e321acad</a><br />
Avatar Hash: d43faaf69d71056793bd037b8d6e321acad985c222d83775610</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzrz1f7tfnro1m0y798wn.webp"><img alt=" " height="323" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fzrz1f7tfnro1m0y798wn.webp" width="800" /></a></p>

<p>Visiting the Gravatar profile reveals a custom avatar and a personalized message left by the room creators:</p>

<p>“Funny thing about email hashes, they follow you places you didn’t expect. Glad you found the right corner of the internet! Here is your prize: VEhNe1MzY3JIVF9QcjBmaWwzX0gf0c19iMzNuX0lkZW50MWZpM2R9"</p>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3gg059zqvzssm4bf5fon.webp"><img alt=" " height="311" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2F3gg059zqvzssm4bf5fon.webp" width="799" /></a></p>

<ol>
<li>Decoding the Flag with CyberChef
The prize string is encoded in Base64. To decode it, we plug the string into CyberChef, apply the From Base64 recipe, and instantly reveal the plaintext flag:</li>
</ol>

<p><a class="article-body-image-wrapper" href="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi3r03vo06jcxfsyva2o6.webp"><img alt=" " height="321" src="https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.us-east-2.amazonaws.com%2Fuploads%2Farticles%2Fi3r03vo06jcxfsyva2o6.webp" width="800" /></a></p>

<p>Input:  VEhNe1MzY3JIVF9QcjBmaWwzX0gf0c19iMzNuX0lkZW50MWZpM2R9<br />
Output: THM{.....................................}<br />
Flag Captured: THM{.................................}</p>

[Read original article on DEV](https://dev.to/mohammadali_30/overheard-at-breakfast-tryhackme-room-write-up-osint-walkthrough-320h)