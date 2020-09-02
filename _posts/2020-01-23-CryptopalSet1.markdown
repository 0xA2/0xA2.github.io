---
layout: post
title: "Cryptopals Challenges Set 1 - Solutions"
description: "[write-up] Written in Python 3 (code only)"
categories: "Cryptopals"
---
<title>Cryptopals Challenges Set 1 - Solutions - LostMyPlaintext</title> 
Challenge 1:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">hex2b64</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64encode(binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(s))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(hex2b64(b</span><span style="color: #e6db74">&#39;49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d&#39;</span><span style="color: #f8f8f2">))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p></p>

Challenge 2: 

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">fixedXOR</span><span style="color: #f8f8f2">(s1,s2):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(s1)</span> <span style="color: #f92672">!=</span> <span style="color: #f8f8f2">len(s2):</span>
		<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;Strings with diferent length!&quot;</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">hex(int(s1,</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">int(s2,</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(fixedXOR(b</span><span style="color: #e6db74">&#39;1c0111001f010100061a024b53535009181c&#39;</span><span style="color: #f8f8f2">,b</span><span style="color: #e6db74">&#39;686974207468652062756c6c277320657965&#39;</span><span style="color: #f8f8f2">))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 3:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">string</span>

<span style="color: #f8f8f2">freqs</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{</span>
    <span style="color: #e6db74">&#39;A&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0651738</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;B&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0124248</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;C&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0217339</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;D&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0349835</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;E&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1041442</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;F&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0197881</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;G&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0158610</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;H&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0492888</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;I&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0558094</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;J&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0009033</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;K&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0050529</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;L&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0331490</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;M&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0202124</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;N&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0564513</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;O&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0596302</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;P&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0137645</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;Q&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0008606</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;R&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0497563</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;S&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0515760</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;T&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0729357</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;U&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0225134</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;V&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0082903</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;W&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0171272</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;X&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0013692</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;Y&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0145984</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39;Z&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0007836</span><span style="color: #f8f8f2">,</span>
    <span style="color: #e6db74">&#39; &#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1918182</span> 
<span style="color: #f8f8f2">}</span>

<span style="color: #f8f8f2">valid</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">string</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">printable[</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">38</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot; &quot;</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">singlebytexor</span><span style="color: #f8f8f2">(s,byte):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(chr(s[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">byte)</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(s)))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">score</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #f8f8f2">points</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">s:</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">valid:</span>
			<span style="color: #f8f8f2">points</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">freqs[ch</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">upper()]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">points</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;3.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;rb&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">f:</span>
		<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(f</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">read())</span>
		<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">256</span><span style="color: #f8f8f2">):</span>
			<span style="color: #f8f8f2">cur_try</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">singlebytexor(ct,i)</span>
			<span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">score(cur_try)</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">&gt;</span> <span style="color: #f8f8f2">max_score:</span>
				<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">cur_score</span>
				<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">singlebytexor(ct,i)</span>
	<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(pt)</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 4:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>

<span style="color: #f8f8f2">freqs</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{</span>
     <span style="color: #e6db74">&#39;A&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0651738</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;B&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0124248</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;C&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0217339</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;D&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0349835</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;E&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1241442</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;F&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0197881</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;G&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0158610</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;H&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0492888</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;I&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0558094</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;J&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0009033</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;K&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0050529</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;L&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0331490</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;M&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0202124</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;N&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0564513</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;O&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0596302</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;P&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0137645</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;Q&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0008606</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;R&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0497563</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;S&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0515760</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;T&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0729357</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;U&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0225134</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;V&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0082903</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;W&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0171272</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;X&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0013692</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;Y&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0145984</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39;Z&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0007836</span><span style="color: #f8f8f2">,</span>
     <span style="color: #e6db74">&#39; &#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1918182</span> 
<span style="color: #f8f8f2">}</span>

<span style="color: #f8f8f2">letters</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;ABCDEFGHIJKLMNOPQRSTUVWXYZ&quot;</span>
<span style="color: #f8f8f2">valid</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">letters</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">letters</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">lower()</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot; &quot;</span> 

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">singlebytexor</span><span style="color: #f8f8f2">(s,byte):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(chr(s[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">byte)</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(s)))</span>


<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">score</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #f8f8f2">points</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">s:</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">valid:</span>
			<span style="color: #f8f8f2">points</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">freqs[ch</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">upper()]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">points</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;4.txt&quot;</span><span style="color: #f8f8f2">,</span> <span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">file:</span>
		<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">lines</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">file:</span>
			<span style="color: #f8f8f2">line</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(lines[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span>
			<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">256</span><span style="color: #f8f8f2">):</span>
				<span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">score(singlebytexor(line,i))</span>
				<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">&gt;</span> <span style="color: #f8f8f2">max_score:</span>
					<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">cur_score</span>
					<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">singlebytexor(line,i)</span>
		<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(ret[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span>
<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 5:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">keyxor</span><span style="color: #f8f8f2">(s,</span> <span style="color: #f8f8f2">key):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">hexlify(</span><span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join([chr(s[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">key[i</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">len(key)])</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(max(len(s),len(key)))])</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode())</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(keyxor(b</span><span style="color: #e6db74">&#39;Burning </span><span style="color: #ae81ff">\&#39;</span><span style="color: #e6db74">em, if you ain</span><span style="color: #ae81ff">\&#39;</span><span style="color: #e6db74">t quick and nimble</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">I go crazy when I hear a cymbal&#39;</span><span style="color: #f8f8f2">,b</span><span style="color: #e6db74">&#39;ICE&#39;</span><span style="color: #f8f8f2">))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 6:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">string</span>

<span style="color: #f8f8f2">MAXSIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">42</span>

<span style="color: #f8f8f2">TOP_SCORING_KEYSIZE_BOUND</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">5</span>

<span style="color: #f8f8f2">freqs</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{</span>
      <span style="color: #e6db74">&#39;A&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0651738</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;B&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0124248</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;C&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0217339</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;D&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0349835</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;E&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1241442</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;F&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0197881</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;G&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0158610</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;H&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0492888</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;I&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0558094</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;J&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0009033</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;K&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0050529</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;L&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0331490</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;M&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0202124</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;N&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0564513</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;O&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0596302</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;P&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0137645</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;Q&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0008606</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;R&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0497563</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;S&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0515760</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;T&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0729357</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;U&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0225134</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;V&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0082903</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;W&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0171272</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;X&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0013692</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;Y&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0145984</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39;Z&#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.0007836</span><span style="color: #f8f8f2">,</span>
      <span style="color: #e6db74">&#39; &#39;</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">0.1918182</span> 
<span style="color: #f8f8f2">}</span>

<span style="color: #f8f8f2">valid</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">string</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">printable[</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">38</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot; &quot;</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">singlebytexor</span><span style="color: #f8f8f2">(s,byte):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(chr(s[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">byte)</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(s)))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">score</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #f8f8f2">points</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">s:</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">ch</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">valid:</span>
			<span style="color: #f8f8f2">points</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">freqs[ch</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">upper()]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">points</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">hamming</span><span style="color: #f8f8f2">(s1,s2):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">len(bin(int(binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">hexlify(s1),</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">int(binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">hexlify(s2),</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">replace(</span><span style="color: #e6db74">&quot;0&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;&quot;</span><span style="color: #f8f8f2">))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">getKeySize</span><span style="color: #f8f8f2">(ct):</span>
	<span style="color: #66d9ef">assert</span> <span style="color: #f8f8f2">MAXSIZE</span> <span style="color: #f92672">&lt;</span> <span style="color: #f8f8f2">len(ct)</span><span style="color: #f92672">//</span><span style="color: #ae81ff">4</span>
	<span style="color: #f8f8f2">dict</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{}</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">,MAXSIZE):</span>
		<span style="color: #f8f8f2">dict[i]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">((hamming(ct[:i],ct[i:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(len(ct)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">2</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">i)])</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">hamming(ct[i:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(len(ct)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)],ct[i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(len(ct)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">3</span><span style="color: #f8f8f2">)])</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">hamming(ct[i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(len(ct)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">3</span><span style="color: #f8f8f2">)],ct[i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">3</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(len(ct)</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">i</span><span style="color: #f92672">*</span><span style="color: #ae81ff">4</span><span style="color: #f8f8f2">)]))</span><span style="color: #f92672">/</span><span style="color: #ae81ff">3</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">/</span><span style="color: #f8f8f2">i</span>
	<span style="color: #f8f8f2">values</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">sorted(dict</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">values())[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:TOP_SCORING_KEYSIZE_BOUND]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">[key</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">key</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">dict</span> <span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">dict[key]</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">values]</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">getBlocks</span><span style="color: #f8f8f2">(ct,key_size):</span>
	<span style="color: #f8f8f2">ret</span><span style="color: #f92672">=</span><span style="color: #f8f8f2">[]</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(key_size)):</span>
		<span style="color: #f8f8f2">temp</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,key_size[i]):</span>
			<span style="color: #f8f8f2">parts</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">b</span><span style="color: #e6db74">&quot;&quot;</span>
			<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">k</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(j,len(ct),key_size[i]):</span>
				<span style="color: #f8f8f2">parts</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">bytes([ct[k]])</span>
			<span style="color: #f8f8f2">temp</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(parts)</span>
		<span style="color: #f8f8f2">ret</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(temp)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">getkey</span><span style="color: #f8f8f2">(ct_blocks):</span>
	<span style="color: #f8f8f2">potential_keys</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">blocks</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">ct_blocks:</span>
		<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">single_block</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">blocks:</span>
			<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
			<span style="color: #f8f8f2">key_part</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span>
			<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">256</span><span style="color: #f8f8f2">):</span>
				<span style="color: #f8f8f2">cur_try</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">singlebytexor(single_block,i)</span>
				<span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">score(cur_try)</span>
				<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">&gt;</span> <span style="color: #f8f8f2">max_score:</span>
					<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">cur_score</span>
					<span style="color: #f8f8f2">key_part</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">chr(i)</span>
			<span style="color: #f8f8f2">key</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">key_part</span>
		<span style="color: #f8f8f2">potential_keys</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(key)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">potential_keys</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">breakReXor</span><span style="color: #f8f8f2">(ct,potential_keys):</span>
	<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">key</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">potential_keys:</span>
		<span style="color: #f8f8f2">potential_pt</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(chr(ct[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">ord(key[i</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">len(key)]))</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(ct)))</span>
		<span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">score(potential_pt)</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">&gt;</span> <span style="color: #f8f8f2">max_score:</span>
			<span style="color: #f8f8f2">max_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">cur_score</span>
			<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">potential_pt</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">pt</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;6.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">f:</span>
		<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64decode(</span><span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">f))</span>
		<span style="color: #f8f8f2">key_size</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">getKeySize(ct)</span>
		<span style="color: #f8f8f2">blocks</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">getBlocks(ct,key_size)</span>
		<span style="color: #f8f8f2">possible_keys</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">getkey(blocks)</span>
		<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">breakReXor(ct,possible_keys)</span>
		<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(pt)</span>
<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 7:

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Cipher</span> <span style="color: #f92672">import</span> <span style="color: #f8f8f2">AES</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">base64</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;7.txt&quot;</span><span style="color: #f8f8f2">,</span> <span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">file:</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">file)</span>
	<span style="color: #f8f8f2">passphrase</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;YELLOW SUBMARINE&quot;</span>
	<span style="color: #f8f8f2">aes</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">new(passphrase</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode(),</span> <span style="color: #f8f8f2">AES</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">MODE_ECB)</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(aes</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decrypt(base64</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">b64decode(s)))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

Challenge 8:

<!-- HTML generated using hilite.me --><div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">BLOCK_SIZE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">16</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">AES_ECB_Score</span><span style="color: #f8f8f2">(line):</span>
	<span style="color: #f8f8f2">blocks</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[line[i:i</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">len(line),</span> <span style="color: #f8f8f2">BLOCK_SIZE</span><span style="color: #f92672">*</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)]</span>
	<span style="color: #f8f8f2">score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">len(blocks)</span> <span style="color: #f92672">-</span> <span style="color: #f8f8f2">len(set(blocks))</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">score</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">detect_AES</span><span style="color: #f8f8f2">(file_content):</span>
	<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[</span><span style="color: #e6db74">&quot;&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">file_content:</span>
		<span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">AES_ECB_Score(line)</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">cur_score</span> <span style="color: #f92672">&gt;</span> <span style="color: #f8f8f2">ret[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]:</span>
			<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[line,cur_score]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;8.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">file:</span>
		<span style="color: #f8f8f2">content</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[line</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">strip(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">file]</span>
		<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Line encrypted with AES &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(detect_AES(content)[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">With a score of &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(detect_AES(content)[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>

<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>

