---
layout: post
title: "Google CTF 2020 - Chunk Norris"
description: "[write-up] The easy crypto challenge from Google CTF 2020"
categories: "Writeups"
---


<style type="text/css">

.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

ul {
  list-style: none;
}
</style>


<p><h4>Writeup for the easy crypto challenge from Google CTF 2020</h4></p>
<hr style="border-top:1px solid #28323C;">
<img class="center" src="/assets/images/chunkNorris.png">
<p><h3>For this challenge we get the following files:</h3></p>
<p>challenge.py</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #75715e">#!/usr/bin/python3 -u</span>

<span style="color: #f92672">import</span> <span style="color: #f8f8f2">random</span>
<span style="color: #f92672">from</span> <span style="color: #f8f8f2">Crypto.Util.number</span> <span style="color: #f92672">import</span> <span style="color: #f92672">*</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">gmpy2</span>

<span style="color: #f8f8f2">a</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xe64a5f84e2762be5</span>
<span style="color: #f8f8f2">chunk_size</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">64</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">gen_prime</span><span style="color: #f8f8f2">(bits):</span>
  <span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">random</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">getrandbits(chunk_size)</span>

  <span style="color: #66d9ef">while</span> <span style="color: #f8f8f2">True:</span>
    <span style="color: #f8f8f2">s</span> <span style="color: #f92672">|=</span> <span style="color: #ae81ff">0xc000000000000001</span>
    <span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
    <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">_</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(bits</span> <span style="color: #f92672">//</span> <span style="color: #f8f8f2">chunk_size):</span>
      <span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(p</span> <span style="color: #f92672">&lt;&lt;</span> <span style="color: #f8f8f2">chunk_size)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">s</span>
      <span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">a</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">%</span> <span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #f8f8f2">chunk_size</span>
    <span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">gmpy2</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">is_prime(p):</span>
      <span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">p</span>

<span style="color: #f8f8f2">n</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gen_prime(</span><span style="color: #ae81ff">1024</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">gen_prime(</span><span style="color: #ae81ff">1024</span><span style="color: #f8f8f2">)</span>
<span style="color: #f8f8f2">e</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">65537</span>
<span style="color: #f8f8f2">flag</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;flag.txt&quot;</span><span style="color: #f8f8f2">,</span> <span style="color: #e6db74">&quot;rb&quot;</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">read()</span>
<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(</span><span style="color: #e6db74">&#39;n =&#39;</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">hex(n))</span>
<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(</span><span style="color: #e6db74">&#39;e =&#39;</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">hex(e))</span>
<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(</span><span style="color: #e6db74">&#39;c =&#39;</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">hex(pow(bytes_to_long(flag),</span> <span style="color: #f8f8f2">e,</span> <span style="color: #f8f8f2">n)))</span>
</pre></div>
<p></p>
<p>output.txt</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">n</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xab802dca026b18251449baece42ba2162bf1f8f5dda60da5f8baef3e5dd49d155c1701a21c2bd5dfee142fd3a240f429878c8d4402f5c4c7f4bc630c74a4d263db3674669a18c9a7f5018c2f32cb4732acf448c95de86fcd6f312287cebff378125f12458932722ca2f1a891f319ec672da65ea03d0e74e7b601a04435598e2994423362ec605ef5968456970cb367f6b6e55f9d713d82f89aca0b633e7643ddb0ec263dc29f0946cfc28ccbf8e65c2da1b67b18a3fbc8cee3305a25841dfa31990f9aab219c85a2149e51dff2ab7e0989a50d988ca9ccdce34892eb27686fa985f96061620e6902e42bdd00d2768b14a9eb39b3feee51e80273d3d4255f6b19</span>
<span style="color: #f8f8f2">e</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0x10001</span>
<span style="color: #f8f8f2">c</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0x6a12d56e26e460f456102c83c68b5cf355b2e57d5b176b32658d07619ce8e542d927bbea12fb8f90d7a1922fe68077af0f3794bfd26e7d560031c7c9238198685ad9ef1ac1966da39936b33c7bb00bdb13bec27b23f87028e99fdea0fbee4df721fd487d491e9d3087e986a79106f9d6f5431522270200c5d545d19df446dee6baa3051be6332ad7e4e6f44260b1594ec8a588c0450bcc8f23abb0121bcabf7551fd0ec11cd61c55ea89ae5d9bcc91f46b39d84f808562a42bb87a8854373b234e71fe6688021672c271c22aad0887304f7dd2b5f77136271a571591c48f438e6f1c08ed65d0088da562e0d8ae2dadd1234e72a40141429f5746d2d41452d916</span>
</pre></div>
<p></p>
<p>From the second file and the last lines of "challenge.py" we can easily tell this is an RSA challenge. As for the rest of challenge.py we only get a "get_prime" function to work with heavily indicating that this is a faulty prime generator that we'll have to break. This function uses a <a href="https://en.wikipedia.org/wiki/Linear_congruential_generator"><b>Linear Congruential Generator</b></a> (or LCG) to generate multiple 64 bit chunks that are seemingly random and then concatenates them until it produces a 1024 bit prime number <b>p</b>. The problem with this approach is that the randomness of each chunk is entirely dependent on the one random value the code generates, stored in the variable <b>s</b>. This means that if we recover <b>s</b> we can recover <b>p</b>. Which we can do with a little bit of math.</p>
<p></p>
<p>We know that in RSA the value <b>n</b> is equal to <b>p*q</b> where <b>p</b> and <b>q</b> are two primes (in this case the two primes being generated by the "gen_prime" function) so we let's write <b>p</b> and <b>q</b> (in relation with the information we have) the following way:</p>
<ul>
	<li><b>p</b> =  s1 * 2<sup>64*15</sup>  +  s1*a mod 2<sup>64</sup> * 2<sup>64*14</sup>  +  s1*a<sup>2</sup> mod 2<sup>64</sup> * 2<sup>64*13</sup>  + ... +  s1*a<sup>14</sup> mod 2<sup>64</sup> * 2<sup>64*1</sup>  +  s1*a<sup>15</sup> mod 2<sup>64</sup></li>
	<li><b>q</b> =  s2 * 2<sup>64*15</sup>  +  s2*a mod 2<sup>64</sup> * 2<sup>64*14</sup>  +  s2*a<sup>2</sup> mod 2<sup>64</sup> * 2<sup>64*13</sup>  + ... +  s2*a<sup>14</sup> mod 2<sup>64</sup> * 2<sup>64*1</sup>  +  s2*a<sup>15</sup> mod 2<sup>64</sup></li>
	<li>Where <b>s1</b> and <b>s2</b> refer to the two different values of <b>s</b> for the two different times the function "get_prime" is called and <b>a</b> is just the number <b>0xe64a5f84e2762be5</b> that we get from the code.</li>
</ul>
<p>Meaning that we can write <b>n</b> as:</p>
<ul>
	<li><b>n</b> = s1*s2*2<sup>64*30</sup> + s1*s2*a<sup>2</sup> mod 2<sup>64</sup> *2<sup>64*29</sup> + ... + s1*s2*a<sup>30</sup> mod 2<sup>64</sup></li>
</ul>
<p>Because of the line "s |= 0xc000000000000001" (which sets the first two bits as well as the last bit of <b>s</b> to <b>1</b>) we can be sure that s1*s2 has a bit length of 128 bits. Knowing this, looking at how we've written <b>n</b> we can conclude that the upper 64 bits of <b>s1*s2</b> are the same as the 64 upper bits of <b>n</b> minus a potential <a href="https://en.wikipedia.org/wiki/Carry_(arithmetic)"><b>carry</b></a>. Now that we have a way to get the 64 upper bits of <b>s1*s2</b> let's figure out how to get the 64 lower bits.</p>
<p>Again, looking at how we've written <b>n</b> we can see that the lower 64 bits of <b>n</b> (or n mod 2<sup>64</sup>) are equal to <b>s1*s2*a<sup>30</sup> mod 2<sup>64</sup></b> so we can conclude the following:</p>
<ul>
	<li>We know that:</li>
	<li>s1*s2*a<sup>30</sup> mod 2<sup>64</sup> = n mod 2<sup>64</sup></li>
	<li>If we multiply both sides by the <a href="https://en.wikipedia.org/wiki/Modular_multiplicative_inverse"><b>Inverse Modulo</b></a> of a<sup>30</sup> modulo 2<sup>64</sup> we get:</li>
	<li>s1*s2 mod 2<sup>64</sup> = n*inverse_modulo(a<sup>30</sup>,2<sup>64</sup>) mod 2<sup>64</sup></li>
	<li>Meaning we can get the lower 64 bits of <b>s1*s2</b> by calculating <b>n*inverse_modulo(a<sup>30</sup>,2<sup>64</sup>) mod 2<sup>64</sup></b></li>
</ul>
<p>Now that we have both the upper 64 bits and the lower 64 bits of <b>s1*s2</b> we can concatenate them to get <b>s1*s2</b> and since this is a number with a bit length of 128 bits we can easily factor it(I used sagemath for this).</p>
<p>First I assumed there was no carry and got the following results:</p>
<img class="center" src="/assets/images/noCarry.png">
<p>This doesn't seem to be correct. Remember <b>s1</b> and <b>s2</b> are random numbers and as such not necessarily prime so it's to be expected that <b>s1*s2</b> will have multiple factors but also remember that <b>s1</b> and <b>s2</b> have a bit length of 64 bits. And with the numbers above it's seems impossible to get even one number with a bit length of 64 by multiplying any given combination of these numbers.</p>
<p>When assuming there is a carry we get the following results:</p>
<img class="center" src="/assets/images/withCarry.png">
<p>Which look far more promosing.</p>
<p>Since we have 10 prime factors of <b>s1*s2</b> what we can do is take every possible combination of these 10 numbers, run each possible combination through the following altered version of the "get_prime" function...</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #66d9ef">def</span> <span style="color: #a6e22e">gen_prime</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #f8f8f2">s</span> <span style="color: #f92672">|=</span> <span style="color: #ae81ff">0xc000000000000001</span>
	<span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">_</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">1024</span><span style="color: #f92672">//</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">):</span>
		<span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(p</span> <span style="color: #f92672">&lt;&lt;</span> <span style="color: #ae81ff">64</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">s</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">a</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">%</span> <span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #ae81ff">64</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">is_prime(p):</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">p</span>
	<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #ae81ff">0</span>
</pre></div>
<p>... and check if any result different from 0 is a factor of <b>n</b>. If it is we just have to divide <b>n</b> by it and we'll get the other one (because again <b>n</b> is the product of two primes). Having the factors of <b>n</b> all we need to do to get the flag is the usual RSA math:</p>
<ul>
	<li>Calculate phi(n) = (p-1)*(q-1)</li>
	<li>(where <b>p</b> and <b>q</b> are factors of <b>n</b>)</li>
	<li>Calculate d = inverse_modulo(e,phi(n))</li>
	<li>And finally calculate flag = c<sup>d</sup> mod n</li>
</ul>
<p>Full solver(written in sage):</p>
<p></p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">sys</span>

<span style="color: #f8f8f2">n</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xab802dca026b18251449baece42ba2162bf1f8f5dda60da5f8baef3e5dd49d155c1701a21c2bd5dfee142fd3a240f429878c8d4402f5c4c7f4bc630c74a4d263db3674669a18c9a7f5018c2f32cb4732acf448c95de86fcd6f312287cebff378125f12458932722ca2f1a891f319ec672da65ea03d0e74e7b601a04435598e2994423362ec605ef5968456970cb367f6b6e55f9d713d82f89aca0b633e7643ddb0ec263dc29f0946cfc28ccbf8e65c2da1b67b18a3fbc8cee3305a25841dfa31990f9aab219c85a2149e51dff2ab7e0989a50d988ca9ccdce34892eb27686fa985f96061620e6902e42bdd00d2768b14a9eb39b3feee51e80273d3d4255f6b19</span>
<span style="color: #f8f8f2">e</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">65537</span>
<span style="color: #f8f8f2">c</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0x6a12d56e26e460f456102c83c68b5cf355b2e57d5b176b32658d07619ce8e542d927bbea12fb8f90d7a1922fe68077af0f3794bfd26e7d560031c7c9238198685ad9ef1ac1966da39936b33c7bb00bdb13bec27b23f87028e99fdea0fbee4df721fd487d491e9d3087e986a79106f9d6f5431522270200c5d545d19df446dee6baa3051be6332ad7e4e6f44260b1594ec8a588c0450bcc8f23abb0121bcabf7551fd0ec11cd61c55ea89ae5d9bcc91f46b39d84f808562a42bb87a8854373b234e71fe6688021672c271c22aad0887304f7dd2b5f77136271a571591c48f438e6f1c08ed65d0088da562e0d8ae2dadd1234e72a40141429f5746d2d41452d916</span>

<span style="color: #f8f8f2">a</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xe64a5f84e2762be5</span>

<span style="color: #75715e">#Assuming no carry</span>
<span style="color: #75715e">#upperBits = bin(n)[2:][0:64]</span>

<span style="color: #75715e">#Assuming carry</span>
<span style="color: #f8f8f2">upperBits</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">bin(int(bin(n)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">-</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span> 

<span style="color: #66d9ef">assert</span> <span style="color: #f8f8f2">gcd(a</span><span style="color: #f92672">**</span><span style="color: #ae81ff">30</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">1</span>

<span style="color: #f8f8f2">lowerBits</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">bin((n</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">inverse_mod(a</span><span style="color: #f92672">^</span><span style="color: #ae81ff">30</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">))</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">(</span><span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">rjust(</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&#39;0&#39;</span><span style="color: #f8f8f2">)</span>

<span style="color: #66d9ef">assert</span> <span style="color: #f8f8f2">len(upperBits)</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">len(lowerBits)</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">64</span>

<span style="color: #f8f8f2">s1s2</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">int(upperBits</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">lowerBits,</span> <span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span>

<span style="color: #f8f8f2">factorList</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(list(factor(s1s2)))</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">gen_prime</span><span style="color: #f8f8f2">(s):</span>
	<span style="color: #f8f8f2">s</span> <span style="color: #f92672">|=</span> <span style="color: #ae81ff">0xc000000000000001</span>
	<span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">_</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">1024</span><span style="color: #f92672">//</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">):</span>
		<span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(p</span> <span style="color: #f92672">&lt;&lt;</span> <span style="color: #ae81ff">64</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">s</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">a</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">s</span> <span style="color: #f92672">%</span> <span style="color: #ae81ff">2</span><span style="color: #f92672">**</span><span style="color: #ae81ff">64</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">is_prime(p):</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">p</span>
	<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #ae81ff">0</span>

<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">num</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">3</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">):</span>
	<span style="color: #f8f8f2">comb</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">Combinations(</span><span style="color: #ae81ff">10</span><span style="color: #f8f8f2">,num)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">list()</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(len(comb)):</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">1</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(num):</span>
			<span style="color: #f8f8f2">s</span> <span style="color: #f92672">*=</span> <span style="color: #f8f8f2">factorList[comb[i][j]][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>
		<span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gen_prime(s)</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">p</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span> <span style="color: #f92672">and</span> <span style="color: #f8f8f2">n</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">p</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">q</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">n</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">p</span>
			<span style="color: #f8f8f2">phin</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(p</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(q</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)</span>
			<span style="color: #f8f8f2">d</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">inverse_mod(e,phin)</span>
			<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">pow(c,d,n)</span>
			<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(hex(pt)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode())</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode())</span>
			<span style="color: #f8f8f2">sys</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">exit()</span>
</pre></div>

<p></p>
<p><b>Flag: CTF{__donald_knuths_lcg_would_be_better_well_i_dont_think_s0__}</b></p>

