---
layout: post
title: "Cryptopals Challenges Set 2 - Solutions"
description: "[write-up] Written in Python 3 (code only)"
---
<p></p>
<p>Challenge 9:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>

<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;YELLOW SUBMARINE&quot;</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(PKCS7_padding(pt))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 10:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">cbc_decrypt</span><span style="color: #f8f8f2">(ct,passphrase,iv):</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(passphrase,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_CBC,</span> <span style="color: #f8f8f2">iv)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decrypt(base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64decode(ct))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;10.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">file:</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">file)</span>
	<span style="color: #f8f8f2">passphrase</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;YELLOW SUBMARINE&quot;</span>
	<span style="color: #f8f8f2">iv</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00</span><span style="color: #e6db74">&quot;</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(cbc_decrypt(s,passphrase,iv))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 11:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">os</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">urandom</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">random</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">randint</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">base64</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">b64encode,</span> <span style="color: #f8f8f2">b64decode</span>

<span style="color: #f8f8f2">KEY_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">IV_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">PLAINTEXT_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">1337</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">genkey</span><span style="color: #f8f8f2">(size):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">urandom(size)</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">appendbytes</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #f8f8f2">prefix</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">randint(</span><span style="color: #ae81ff">5</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">suffix</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">randint(</span><span style="color: #ae81ff">5</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">urandom(prefix)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">urandom(suffix)</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">genIV</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">urandom(IV_SIZE)</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">encrypt</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">genkey(KEY_SIZE)</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">PKCS7_padding(pt)</span>
	<span style="color: #f8f8f2">iv</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">genIV()</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">randint(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
		<span style="color: #f8f8f2">mode</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;ECB&quot;</span>
	<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_CBC,</span> <span style="color: #f8f8f2">iv)</span>
		<span style="color: #f8f8f2">mode</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;CBC&quot;</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b64encode(aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encrypt(pt))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(ct,mode)</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">AES_ECB_Score</span><span style="color: #f8f8f2">(ct):</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b64decode(ct)</span>
	<span style="color: #f8f8f2">blocks</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[ct[i:i</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">BLOCK_SIZE]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">len(ct),</span> <span style="color: #f8f8f2">BLOCK_SIZE)]</span>
	<span style="color: #f8f8f2">score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">len(blocks)</span> <span style="color: #f92672">-</span> <span style="color: #f8f8f2">len(set(blocks))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">score</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">appendbytes(urandom(PLAINTEXT_SIZE))</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">encrypt(pt</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;A&quot;</span><span style="color: #f92672">*</span><span style="color: #ae81ff">1337</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Ciphertext &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(ct[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">AES mode used &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(ct[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">AES_ECB_Score(ct[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">&gt;</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Guessed mode &gt; ECB&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Guessed mode &gt; CBC&quot;</span><span style="color: #f8f8f2">)</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&#39;__main__&#39;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 12:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">os</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">urandom</span>

<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">string</span>

<span style="color: #f8f8f2">KEY_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">IV_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">HOW_MANY_BLOCKS</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">9</span>
<span style="color: #f8f8f2">PT_LEN</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">HOW_MANY_BLOCKS</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">BLOCK_SIZE</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">enc</span><span style="color: #f8f8f2">(pt,key):</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">PKCS7_padding(pt)</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encrypt(pt)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ct</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">brute_force_ECB</span><span style="color: #f8f8f2">(pt,key):</span>
	<span style="color: #f8f8f2">known_bytes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;&quot;</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,PT_LEN):</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">byte</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">string</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">printable</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode():</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">enc(b</span><span style="color: #e6db74">&#39;A&#39;</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(PT_LEN</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span>  <span style="color: #f92672">-</span> <span style="color: #f8f8f2">i)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">known_bytes</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr(byte)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode(),key)[:(PT_LEN</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)]</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">enc(b</span><span style="color: #e6db74">&#39;A&#39;</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(PT_LEN</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span>  <span style="color: #f92672">-</span> <span style="color: #f8f8f2">i)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt,key)[:(PT_LEN</span> <span style="color: #f92672">-</span><span style="color: #ae81ff">1</span> <span style="color: #f8f8f2">)]:</span>
				<span style="color: #f8f8f2">known_bytes</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">chr(byte)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span>
				<span style="color: #66d9ef">break</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">known_bytes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode()</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64decode(</span><span style="color: #e6db74">&#39;&#39;&#39;Um9sbGluJyBpbiBteSA1LjAKV2l0aCBteSByYWctdG9wIGRvd24gc28gbXkgaGFpciBjYW4gYmxvdwpUaGUgZ2lybGllcyBvbiBzdGFuZGJ5IHdhdmluZyBqdXN0IHRvIHNheSBoaQpEaWQgeW91IHN0b3A/IE5vLCBJIGp1c3QgZHJvdmUgYnkK&#39;&#39;&#39;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">urandom(KEY_SIZE)</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(brute_force_ECB(pt,key))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&#39;__main__&#39;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 13:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">os</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">urandom</span>

<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">random</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">re</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">sys</span>

<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span><span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">KEY_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>

<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">urandom(KEY_SIZE)</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">enc</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">PKCS7_padding(pt)</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encrypt(pt)</span>


<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">dec</span><span style="color: #f8f8f2">(ct):</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decrypt(ct)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode()</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">parser</span><span style="color: #f8f8f2">(cookie):</span>
	<span style="color: #f8f8f2">parts</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">cookie</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">split(</span><span style="color: #e6db74">&quot;&amp;&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">dict</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{}</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">part</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">parts:</span>
		<span style="color: #f8f8f2">key_value</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">part</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">split(</span><span style="color: #e6db74">&quot;=&quot;</span><span style="color: #f8f8f2">)</span>
		<span style="color: #f8f8f2">dict[key_value[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">key_value[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">dict</span>


<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">profile</span><span style="color: #f8f8f2">(email):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">re</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">match(</span><span style="color: #e6db74">&quot;.[&amp;=].&quot;</span><span style="color: #f8f8f2">,email):</span>
		<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;Invalid characters&quot;</span>
	<span style="color: #f8f8f2">profile</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{</span>
		<span style="color: #e6db74">&#39;email&#39;</span><span style="color: #f8f8f2">:email,</span>
		<span style="color: #e6db74">&#39;uid&#39;</span><span style="color: #f8f8f2">:</span><span style="color: #ae81ff">1337</span><span style="color: #f8f8f2">,</span>
		<span style="color: #e6db74">&#39;role&#39;</span><span style="color: #f8f8f2">:</span><span style="color: #e6db74">&#39;user&#39;</span><span style="color: #f8f8f2">,</span>
	<span style="color: #f8f8f2">}</span>
	<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;email={}&amp;uid={}&amp;role={}&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">format(profile[</span><span style="color: #e6db74">&quot;email&quot;</span><span style="color: #f8f8f2">],profile[</span><span style="color: #e6db74">&quot;uid&quot;</span><span style="color: #f8f8f2">],profile[</span><span style="color: #e6db74">&quot;role&quot;</span><span style="color: #f8f8f2">])</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">ECB_cut_and_paste</span><span style="color: #f8f8f2">(profile,newrole):</span>
	<span style="color: #66d9ef">return</span>  <span style="color: #f8f8f2">dec(enc(profile)[:</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">enc(</span><span style="color: #e6db74">&quot;admin&quot;</span><span style="color: #f8f8f2">))[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">11</span><span style="color: #f8f8f2">]</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">email</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;42@mail.com&quot;</span>
	<span style="color: #75715e">#Since len(email) = 11 the second block of enc(profile(email)) will end in &quot;role=&quot;</span>
	<span style="color: #75715e">#To create a valid admin profile we&#39;ll take the first 2 blocks of enc(profile(email)) and concat enc(&quot;admin&quot;)</span>
	<span style="color: #75715e">#The decrypted result will be a valid admin profile</span>
	<span style="color: #75715e">#block1			 	block2          	block3</span>
	<span style="color: #75715e">#email=42@mail.co	m&amp;uid=1337&amp;role= 	admin(padding)</span>
	<span style="color: #f8f8f2">admin_profile</span> <span style="color: #f92672">=</span>  <span style="color: #f8f8f2">parser(ECB_cut_and_paste(profile(email),</span><span style="color: #e6db74">&quot;admin&quot;</span><span style="color: #f8f8f2">))</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Registered the email &gt; {}</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Correspondent uid &gt; {}</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Respective role &gt; {}&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">format(admin_profile[</span><span style="color: #e6db74">&quot;email&quot;</span><span style="color: #f8f8f2">],admin_profile[</span><span style="color: #e6db74">&quot;uid&quot;</span><span style="color: #f8f8f2">],admin_profile[</span><span style="color: #e6db74">&quot;role&quot;</span><span style="color: #f8f8f2">]))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&#39;__main__&#39;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 14:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">os</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">urandom</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">random</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">randint</span>

<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">string</span>

<span style="color: #f8f8f2">KEY_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">IV_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">HOW_MANY_BLOCKS</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">9</span>
<span style="color: #f8f8f2">PT_LEN</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">HOW_MANY_BLOCKS</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">BLOCK_SIZE</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">enc</span><span style="color: #f8f8f2">(pt,key):</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">PKCS7_padding(pt)</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encrypt(pt)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ct</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">brute_force_ECB</span><span style="color: #f8f8f2">(pt,key):</span>
	<span style="color: #f8f8f2">known_bytes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;&quot;</span>
	<span style="color: #f8f8f2">flag</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">enc(b</span><span style="color: #e6db74">&quot;STIMULUSRESPONSE&quot;</span><span style="color: #f8f8f2">,key)</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,PT_LEN):</span>
		<span style="color: #f8f8f2">cur_try</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;&quot;</span>
		<span style="color: #66d9ef">while</span> <span style="color: #f8f8f2">True:</span>
			<span style="color: #f8f8f2">cur_try</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">enc(urandom(randint(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">255</span><span style="color: #f8f8f2">))</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;STIMULUSRESPONSE&quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;A&quot;</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(PT_LEN</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span> <span style="color: #f92672">-</span> <span style="color: #f8f8f2">i)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt,key)</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">flag</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">cur_try:</span>
				<span style="color: #66d9ef">break</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">byte</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">string</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">printable</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode():</span>
			<span style="color: #66d9ef">while</span> <span style="color: #f8f8f2">True:</span>
				<span style="color: #f8f8f2">cur_byte</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">enc(urandom(randint(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">255</span><span style="color: #f8f8f2">))</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;STIMULUSRESPONSE&quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;A&quot;</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(PT_LEN</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">i)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">known_bytes</span>  <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr(byte)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode(),key)</span>
				<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">flag</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">cur_byte:</span>
					<span style="color: #66d9ef">break</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_byte</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">split(flag)[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][:PT_LEN</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">cur_try</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">split(flag)[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][:PT_LEN</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]:</span>
				<span style="color: #f8f8f2">known_bytes</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">chr(byte)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span>
				<span style="color: #66d9ef">break</span>

	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">known_bytes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode()</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64decode(</span><span style="color: #e6db74">&#39;&#39;&#39;Um9sbGluJyBpbiBteSA1LjAKV2l0aCBteSByYWctdG9wIGRvd24gc28gbXkgaGFpciBjYW4gYmxvdwpUaGUgZ2lybGllcyBvbiBzdGFuZGJ5IHdhdmluZyBqdXN0IHRvIHNheSBoaQpEaWQgeW91IHN0b3A/IE5vLCBJIGp1c3QgZHJvdmUgYnkK&#39;&#39;&#39;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">urandom(KEY_SIZE)</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(brute_force_ECB(pt,key))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&#39;__main__&#39;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 15:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding_validation</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(pt)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">0</span> <span style="color: #f92672">and</span> <span style="color: #f8f8f2">pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">&gt;=</span> <span style="color: #f8f8f2">BLOCK_SIZE:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;Valid padding &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt</span>
	<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">chr(pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">pt[</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]:]</span> <span style="color: #f92672">and</span> <span style="color: #f8f8f2">pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">&lt;</span> <span style="color: #f8f8f2">BLOCK_SIZE:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;Valid padding, plaintext &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">strip_padding(pt)</span>
	<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;Invalid padding!&quot;</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">strip_padding</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">pt[:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">pt[len(pt)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]]</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;YELLOW SUBMARINE&quot;</span> 
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(PKCS7_padding_validation(pt))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>Challenge 16:</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">os</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">urandom</span>

<span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">KEY_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>
<span style="color: #f8f8f2">IV_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>

<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">urandom(KEY_SIZE)</span>
<span style="color: #f8f8f2">iv</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">urandom(IV_SIZE)</span>

<span style="color: #f8f8f2">prefix</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;comment1=cooking%20MCs;userdata=&quot;</span>
<span style="color: #f8f8f2">suffix</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;;comment2=%20like%20a%20pound%20of%20bacon&quot;</span> 

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">filter_and_pad</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">pt</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">replace(b</span><span style="color: #e6db74">&quot;;&quot;</span><span style="color: #f8f8f2">,b</span><span style="color: #e6db74">&quot;%&quot;</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">replace(b</span><span style="color: #e6db74">&quot;=&quot;</span><span style="color: #f8f8f2">,b</span><span style="color: #e6db74">&quot;%&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">prefix</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">suffix</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PKCS7_padding</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">chr((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">BLOCK_SIZE)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">((BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(len(s)</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">len(s))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">s</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">encrypt</span><span style="color: #f8f8f2">(pt):</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">PKCS7_padding(pt)</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_CBC,</span> <span style="color: #f8f8f2">iv)</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encrypt(pt)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ct</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">cbc_decrypt</span><span style="color: #f8f8f2">(ct):</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(key,</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_CBC,</span> <span style="color: #f8f8f2">iv)</span>
	<span style="color: #f8f8f2">dec</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decrypt(ct)</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;;admin=true;&quot;</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">dec:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">True</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">False</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">CBC_bitflipping_attack</span><span style="color: #f8f8f2">(ct):</span>
	<span style="color: #f8f8f2">semicolon</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">ct[len(prefix)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">^</span> <span style="color: #f8f8f2">ord(</span><span style="color: #e6db74">&quot;%&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">^</span> <span style="color: #f8f8f2">ord(</span><span style="color: #e6db74">&quot;;&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">equals</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">ct[len(prefix)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">^</span> <span style="color: #f8f8f2">ord(</span><span style="color: #e6db74">&quot;%&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">^</span> <span style="color: #f8f8f2">ord(</span><span style="color: #e6db74">&quot;=&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ct[:len(prefix)</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">16</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">bytes([semicolon])</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">ct[len(prefix)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">15</span><span style="color: #f8f8f2">:len(prefix)</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">10</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">bytes([equals])</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">ct[len(prefix)</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">9</span><span style="color: #f8f8f2">:]</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;;admin=true&quot;</span>
	<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">encrypt(filter_and_pad(pt))</span>
	<span style="color: #f8f8f2">alt_ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(CBC_bitflipping_attack(ct))</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(cbc_decrypt(alt_ct))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>