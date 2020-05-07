---
layout: post
title: "Merkle-Hellman knapsack and the LLL algorithm"
description: "[project] An implementation of the original Merkle-Hellman knapsack cryptosystem and the algorithm that can break it in polynomial time"
categories: "Project"
---

<p>An implementation of the original Merkle-Hellman knapsack cryptosystem and the algorithm that can break it in polynomial time.</p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>Content:</h3></p>
<ul>
	<li><a href="#Motivation">Motivation</a></li>
	<li><a href="#Merkle-Hellman">Merkle-Hellman Knapsack</a></li>
	<li><a href="#LLL">LLL Algorithm</a></li>
	<li><a href="#Implementation">About the implementation</a></li>
	<li><a href="#Code">The actual code</a></li>
</ul>
<p><a name="Motivation"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>Motivation:</h3></p>
<p></p>
<p>A few months back I dug up an old Twitter account and thought it'd be fun to post nothing but encrypted messages using previously broken encryption methods and have friends and colleges try to recover the original plaintexts. After running through some of the more well known classic ciphers I figured I should try something more interesting and remembered reading about the Merkle-Hellman knapsack cryptosystem sometime last year. However, at this point I only vaguely remembered how it worked and even though I new it had been broken in the 80's by <a href="https://en.wikipedia.org/wiki/Adi_Shamir">Adi Shamir</a>, I didn't know the exact method he used to do it. So I decided to do some digging and thought that it would be fun (as well as a good learning method) to implement a version of the original Merkle-Hellman cryptosystem including an option to break a given ciphertext using Shamir's method.</p>
<p><a name="Merkle-Hellman"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>Merkle-Hellman Knapsack Cryptosystem</h3></p>
<p></p>
<p>Making use of elementary math in a pretty clever way, the Merkle-Hellman knapsack, created by <a href="https://en.wikipedia.org/wiki/Ralph_Merkle">Ralph Merkle</a> and <a href="https://en.wikipedia.org/wiki/Martin_Hellman">Matin Hellman</a> in 1978, was among the first proposed public key cryptosystems. It's security relies on the <a href="https://en.wikipedia.org/wiki/Subset_sum_problem">subset sum problem</a> (or knapsack problem) which is known to be <a href="https://en.wikipedia.org/wiki/NP-complete">NP-Complete</a> and can be described as follows:</p>
<p>Say you have a set (or knapsack) <b>A</b> of <b>n</b> seemingly random integers and some other integer <b>x</b> that is not in set <b>A</b>. Assuming it is possible, find a combination of elements of <b>A</b> that sum up to <b>x</b>. Or to put it in a more interesting way: assuming it is possible, find a set <b>B</b>, also of size <b>n</b>, where <b>B<sub>i</sub></b> &#8712; {0,1}, such that <b>sum(A<sub>0</sub>B<sub>0</sub>+A<sub>1</sub>B<sub>1</sub>+...+A<sub>n-1</sub>B<sub>n-1</sub>) = x. </b></p>
<p>When you think of this problem considering the second description you might get an idea of how it could be used to cipher a message: You convert your message to binary and use that as your set <b>B</b> then you generate a set <b>A</b> of equal size and compute <b>sum(A<sub>0</sub>B<sub>0</sub>+A<sub>1</sub>B<sub>1</sub>+...+A<sub>n-1</sub>B<sub>n-1</sub>) = x. </b> And now if you use <b>x</b> as your ciphertext any potential attacker will have great dificulty discovering what your original message was. However, this also means it will be very hard for the person to whom you want to send a secret message to read the original plaintext if they only know the value <b>x</b> and the knapsack <b>A</b>. Consider the following example:</p>

<p>Say you're presented with <b>A = [1875, 621, 1660, 606, 170, 1382, 262, 521]</b> and <b>x = 2802</b>. Since <b>A</b> is quite small in this example you can probably figure out that <b>B = [0,1,1,0,0,0,0,1]</b>, which is binary for "a". However, notice that it would be very hard, if not impossible, to do the same for a larger <b>A</b> in a significantly efficient manner (if this is not clear do take some time to ponder on this problem).</p> 

<p>To figure out how we can create a public-key cryptosystem out of this we first need to talk about superincreasing knapsacks: meaning that every element of the knapsack is greater than the sum of all previous elements. If your knapsack has this property you can actually recover <b>B</b> in polynomial time only knowing <b>A</b> and <b>x</b>, meaning that the subset sum problem can be solved in an efficient way for superincreasing knapsacks. Let's examine the follwing example:</p>

<p>Consider the superincreasing knapsack <b>w = [9, 15, 28, 60, 116, 236, 466, 941]</b> as well as <b>x = 509</b>. You can recover <b>B</b> with the following algorithm<sup>1</sup>:<a name="1"></a></p>
<p></p>

<pre><h4>1| B = []
2| for i from 7 to 0 do:
3| 	 if w<sub>i</sub> <= x then:
4|		 x = x - w<sub>i</sub>
5|		 B.appendLeft(1)
6|	 else:
7|		 B.appendLeft(0)
8| return B</h4></pre>

<p></p>
<p>Knowing this, Merkle and Hellman figured they could use <a href="https://en.wikipedia.org/wiki/Modular_arithmetic">modular arithmetic</a> to transform a superincreasing knapsack into a general knapsack so that the former, as well the transformation process, could be used as a private key and the latter as a public key. If Alice wants to receive secret messages from Bob she first selects a super increasing knapsack <b>w</b> and applies this transformation to obtain a seemingly general knapsack <b>A</b>. Then she makes <b>A</b> public and has Bob compute <b>sum(A<sub>0</sub>B<sub>0</sub>+A<sub>1</sub>B<sub>1</sub>+...+A<sub>n-1</sub>B<sub>n-1</sub>) = x. </b> (again, we'll refer to the plaintext message in binary as set "<b>B</b>"). Finally Bob sends the ciphertext <b>x</b> to Alice who applies the inverse of the transformation to it and uses the result along side the superincrasing knapsack to solve the easier version of the subset sum problem, obtaining the plaintext message. In this scenario, Alice can easily decrypt the message, because she knows how to apply the transformation while an attacker, only knowing <b>x</b> and <b>A</b>, will apparently need to solve the knapsack problem, known to be hard.</p>

<p>But how does this transformation work exactly?</p>
<p>As mentioned before, it is done through <a href="https://en.wikipedia.org/wiki/Modular_arithmetic">modular arithmetic</a>. After selecting a superincreasing sequence <b>w</b> Alice further selects a modulo <b>q</b> with the follwing property: <b>q</b> > <b>(w<sub>0</sub>+w<sub>1</sub>+...+w<sub>n-1</sub>)</b> (where <b>n</b> is the lenght of <b>w</b>). Furthermore, Alice selects a coefficient <b>r</b> such that <b>q</b> and <b>r</b> are coprime, in other words, such that <b>gcd(q,r) = 1</b>. Alice's private key will consist of the knapsack <b>w</b> and the two values <b>q</b> and <b>r</b>. To generate her public key Alice computes <b>r*w<sub>i</sub> mod q (for i from 0 to n-1)</b> and the resulting set of values will become her general knapsack <b>A</b>. Consider the follwing example:</p>

<p>Alice generates the superincresing knapsack <b>w = [3, 10, 24, 46, 88, 175, 352, 707]</b>, selects the values: </p>
<p><b>q = 1409 > 3+10+24+46+88+175+352+707</b> and <b>r = 831 (gcd(1409,831) = 1)</b> and keeps these to herself. Then she computes the values for the public knapsack <b>A</b>:</p>
<p>831*3   mod 1409 = 1084</p>
<p>831*10  mod 1409 = 1265</p>
<p>831*24  mod 1409 = 218</p>
<p>831*46  mod 1409 = 183</p>
<p>831*88  mod 1409 = 1269</p>
<p>831*175 mod 1409 = 298</p>
<p>831*352 mod 1409 = 849</p>
<p>831*707 mod 1409 = 1373</p>
<p>And finally makes <b>A = [1084, 1265, 218, 183, 1269, 298, 849, 1373]</b> public.</p>
<p>Now suppose Bob wants to send Alice the message "01100011" (binary form of "c"). He first computes 1084*0+1265*1+218*1+183*0+1269*0+298*0+849*1+1373*1 = 3705 and sends this result to Alice.</p>
<p>On her end Alice calculates <b>I = r inverse modulo q</b>, in other words, a value <b>I</b> such that <b>r*I &#8773; 1 mod q </b>. Now she can inverse the tranformation by calculating <b>3705*I mod q</b>. By using the result (let's call it <b>x</b>) along side her private superincreasing knapsack she can retrieve the plaintext message using the same <a href="#1">algorithm<sup>1</sup></a> presented above. Obviously the message in this example is very short (making this specific ciphertext quite vulnerable because of it) however this whole process seems to make it so that a potential attacker (only knowing <b>A</b> and the ciphertext) would have to solve the general subset problem (or general knapsack problem) in order to recover the plaintext. However, this is not necessarily the case.</p>
<p><a name="LLL"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>LLL Algorithm:</h3></p>
<p></p>
<p>To understand how we can break the Merkle-Hellman knapsack cryptosystem we first need o understand what lattices are (note that you'll need some elementary Linear Algebra knowledge to follow along).</p>
<p>We can define a lattice <b>L</b> as the ℤ-linear span of a set of <b>n</b> linearly independent vectors. Or to put it more simply:</p>
<p><h4><b>L</b> = {a<sub>1</sub>v<sub>1</sub>+a<sub>2</sub>v<sub>2</sub>+...+a<sub>n</sub>v<sub>n</sub> where a<sub>i</sub> &#8712; ℤ}</h4></p>
<p>The vectors v<sub>1</sub>,...,v<sub>n</sub> form a basis of <b>L</b>.</p>
<p>Geometrically, a lattice in R<sup>2</sup> would, for example, look somthing like the following:</p>

<img src="/assets/images/plot.png">

<p>Now let's analyze how we can relate this to breaking the Merkle-Hellman knapsack.</p>

<p>Suppose you have a 1xN matrix <b>X</b> and 1x1 matrix <b>Y</b> and want to figure ou the Nx1 solution matrix <b>S</b> to the matrix equation: <b>XS = Y</b>, where the entries of <b>S</b> can only be 0 or 1 (in the context of the Merkle-Hellman knapsack, <b>S</b> would be the plaintext the attacker is after).</p>
<p>Now consider the follwing matrices:</p>
<pre>
<p></p>
    [ 1   0   ...  0    0   ]
    [ 0   1   ...  0    0   ]
    [ .   .   ...  .    .   ]
<b>M</b> = [ .   .   ...  .    .   ]
    [ .   .   ...  .    .   ]
    [ 0   0   ...  1    0   ]
    [ X<sub>1,1</sub> X<sub>1,2</sub> ... X<sub>1,n</sub> -Y<sub>1,1</sub> ] 
<p></p>
    [ S<sub>1,1 </sub>  ]
    [ S<sub>2,1 </sub>  ]
    [   .   ]
<b>K</b> = [   .   ]
    [   .   ]
    [ S<sub>n,1 </sub>  ]
    [   1   ]
<p></p>
    [ S<sub>1,1 </sub>  ]
    [ S<sub>2,1 </sub>  ]
    [   .   ]
<b>C</b> = [   .   ]
    [   .   ]
    [ S<sub>n,1 </sub>  ]
    [   0   ]
<p></p>
</pre>

<p>Note that if <b>S</b> is a solution to the matrix equation <b>XS = Y</b> then the matrix equation <b>MK = C</b> holds true.</p>
<p>Now consider <b>m<sub>1</sub>,m<sub>2</sub>,...,m<sub>n</sub></b> to be the columns of <b>M</b>. We can write <b>C</b> as <b>C = K<sub>1,1</sub>m<sub>1</sub>+K<sub>2,1</sub>m<sub>2</sub>...K<sub>n,1</sub>m<sub>n</sub></b>. Meaning that <b>C</b> is in fact the lattice spanned by the columns of <b>M</b>.</p>

<p>Now further note that because the entries of <b>C</b> are either 0 or 1 the <a href="https://en.wikipedia.org/wiki/Euclidean_vector#Length">Euclidean vector lenght</a> of the vector <b>C</b> will be quite short:</p>
<p>&#8214; <b>C</b> &#8214; = sqrt(<b>S</b><sub>1</sub><sup>2</sup>,<b>S</b><sub>2</sub><sup>2</sup>...<b>S</b><sub>n</sub><sup>2</sup>) <= sqrt(n)</p>

<p>And if we were to calculate <b>C</b> then we would also know <b>S</b>, in other words we would know the binary form of the plaintext.</p>

<p>This is where the <b>LLL algorithm</b> (created by <a href="https://en.wikipedia.org/wiki/Arjen_Lenstra">Arjen Lenstra</a>, <a href="https://en.wikipedia.org/wiki/Hendrik_Lenstra">Hendrik Lenstra</a> and <a href="https://en.wikipedia.org/wiki/L%C3%A1szl%C3%B3_Lov%C3%A1sz">László Lovász</a>) comes in. Given a basis for a lattice as input, this algorithm calculates a reduced basis ( a basis with short, and close to orthogonal, vectors) of the same lattice.</p>

<p>If we use the matrix <b>M</b> as input, the LLL algorithm will output short vectors in the lattice spanned by the columns of <b>M</b> meaning that (although not guaranteed) that there is a good enough chance that among those vectors we will find <b>C</b></p>

<p>Consider the following example:</p>
<p>Suppose Alice publishes the following public key: <b>A = [367, 272, 1753, 708, 17, 1623, 1562, 978]</b>. Bob wants to send the message "01100001" to Alice and uses this public key to encrypt it obtaning the following ciphertext: <b>ct = 3003</b>.</p>
<p>Only knowing Alice's public key <b>A</b> and the ciphertext <b>ct</b> and attacker first constructs the following matrix:</p>
<pre>
	[  1   0    0   0  0    0    0   0     0]
	[  0   1    0   0  0    0    0   0     0]
	[  0   0    1   0  0    0    0   0     0]
	[  0   0    0   1  0    0    0   0     0]
M =	[  0   0    0   0  1    0    0   0     0]
	[  0   0    0   0  0    1    0   0     0]
	[  0   0    0   0  0    0    1   0     0]
	[  0   0    0   0  0    0    0   1     0]
	[367 272 1753 708 17 1623 1562 978 -3003]
</pre>
<p>Then applies the LLL algorithm using it as input, obtaining the follwing result:</p>
<pre>
         [0  0  2  1  0 -2 -1 -2 -1] 
         [1 -1  0  0 -1 -1  1  0 -1]
         [1  0  0  0  1 -1  0  1  2]
         [0 -1  1  0  1  0 -1  2 -1]
LLL(M) = [0  0  0  2  1  0  0 -1  1]
         [0  0  0  1 -1 -2 -2  0  2]
         [0  0  1  0 -1  0  0  1  1]
         [1  1  0  1  1  0 -2 -1 -1]
         [0 -2  1 -1 -1  1  1 -1  0]
</pre>

<p>Notice how the first eight entries of the first column of the resulting matrix are thr precise message Bob encrypted: "01100001" (which is binary for "a"). Thus the attacker has recovered the original plaintext using nothing but public information.</p>

<p>The LLL algorithm:</p>
<pre><h4>
LLL(M):	
1 | V = GS(M) # "GS" refers to the Gram-Schmidt process(**)
2 | W<sub>i,j</sub> = inner_product(M<sub>i</sub>,V<sub>j</sub>)/inner_product(V<sub>i</sub>,V<sub>j</sub>) # where the values of i and j are the most current 
3 | h = 1
4 | while h <= n: # n refers to the number of elemts in M
5 |    for j from h-1 to 0:
6 |       if abs(W<sub>h,j</sub>) > 1/2:
7 |          M<sub>h</sub> = M<sub>h</sub> - round(W<sub>hj</sub>)*M<sub>j</sub>
8 |    V = GS(M) # update V and W
9 |    W<sub>ij</sub> = inner_product(M<sub>i</sub>,V<sub>j</sub>)/inner_product(V<sub>i</sub>,V<sub>j</sub>) # where the values of i and j are the most current 
10|    if inner_product(V<sub>h</sub>,V<sub>h</sub>) >= (0.99(***)-(W<sub>h,h-1</sub>)<sup>2</sup>)*inner_product(V<sub>h-1</sub>,V<sub>h-1</sub>):
11|       h = h + 1
12|    else:
13|       swap(M<sub>h</sub>,M<sub>h-1</sub>)
14|       V = GS(M) # update V and W
15|       W<sub>ij</sub> = inner_product(M<sub>i</sub>,V<sub>j</sub>)/inner_product(V<sub>i</sub>,V<sub>j</sub>) # where the values of i and j are the most current 
16|       h = max(h-1,1)
17| return M
</h4></pre>
<p>(**)The <a href="https://en.wikipedia.org/wiki/Gram%E2%80%93Schmidt_process">Gram-Schmidt process</a> returns an orthogonal basis for the subspace spanned by the columns of <b>M</b>. (***)The value 0.99 is chosen for the Lovász Condition to obtain a strong reduction.</p>

<p><a name="Implementation"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>About the implementation:</h3></p>
<p>A few notes and clarifications before presenting the code:</p>
<ul>
	<li>This implementation is based on the original knapsack cryptosystem proposed by Merkle and Hellman, if you found this interesting you might want to read on other variations of cryptosystems based on the knapsack problem.</li>
	<li>For this implementation the public and private knapsacks always have the same size (in bits) as the plaintext. You could also use knapsacks that are shorter and encrypt a given plaintext by blocks.</li>
	<li>One thing you can do to make this cryptosystem slightly stronger is to permutate the public key values. Note that although this was not implemented it would not prevent it from being broken.</li>
	<li>Although I wrote this program so that it can be run with python3, the "break cipher" feature will not work properly if you do so. The intended way is to run it with <b><a href="https://www.sagemath.org/">sagemath</a></b>. This is to avoid problems with floating point overflows.</li>
	<li>A lot of sagemath features were purposely not used to better illustrate how certain processes work, namely the LLL algorithm.</li>
	<li>Code can also be found on my github: <a href="https://github.com/0xA2/Merkle-Hellman-Knapsack-and-LLL/blob/master/knapsack.sage">https://github.com/0xA2/Merkle-Hellman-Knapsack-and-LLL/blob/master/knapsack.sage</a></li>
</ul>
<p><a name="Code"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>The actual code:</h3></p>
<p></p>

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #75715e">####################################################################</span>
<span style="color: #75715e">#                                                                  #</span>
<span style="color: #75715e">#         ### Merkle-Hellman Knapsack Sage 9.0 Version ###         #</span>
<span style="color: #75715e">#                                                                  #</span>
<span style="color: #75715e">#  WARNING: I wrote this program to be compatible with Python 3,   #</span>
<span style="color: #75715e">#           however the &#39;break cipher&#39; feature is only fully       #</span>
<span style="color: #75715e">#           availiable when ran with sagemath to avoid floating	   #</span>
<span style="color: #75715e">#           point limitations. Also, as I&#39;m sure you can tell,     #</span>
<span style="color: #75715e">#           this cryptosystem can be easily broken and should      #</span>
<span style="color: #75715e">#           in no circumstance be considered for any serious       #</span>
<span style="color: #75715e">#           cryptographic use.                                     #</span>
<span style="color: #75715e">#                                                                  #</span>
<span style="color: #75715e">####################################################################</span>

<span style="color: #f92672">from</span> <span style="color: #f8f8f2">random</span> <span style="color: #66d9ef">import</span> <span style="color: #f8f8f2">SystemRandom</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">binascii</span>

<span style="color: #75715e"># Private key randomness coefficients</span>
<span style="color: #75715e"># (NOTE: For demonstration purposes I kept these values very low in order to artificially increase the program&#39;s efficiency, in an ideal scenario they should be much higher!)</span>
<span style="color: #f8f8f2">W_RANGE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">10</span>
<span style="color: #f8f8f2">Q_RANGE</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">10</span>

<span style="color: #f8f8f2">banner</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&#39;&#39;&#39;-------------------------------</span>
<span style="color: #e6db74">    Merkle-Hellman Knapsack</span>
<span style="color: #e6db74">-------------------------------&#39;&#39;&#39;</span>

<span style="color: #75715e"># Encryption related functions - start</span>

<span style="color: #75715e"># Verify if public key has a valid length</span>
<span style="color: #75715e"># (NOTE: in this implementation key-size is always equal to plaintext length in bits)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">verify_publickey</span><span style="color: #f8f8f2">(pt,public_key):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">len(</span><span style="color: #e6db74">&quot;&quot;</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">join(format(ord(c),</span><span style="color: #e6db74">&#39;b&#39;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">c</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">pt)</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">rjust(len(pt)</span><span style="color: #f92672">*</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;0&quot;</span><span style="color: #f8f8f2">))</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">len(public_key)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">encrypt</span><span style="color: #f8f8f2">(pt,public_key):</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">str(sum([(int(bin(int(binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">hexlify(pt</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode()),</span><span style="color: #ae81ff">16</span><span style="color: #f8f8f2">))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">rjust(len(pt)</span><span style="color: #f92672">*</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;0&quot;</span><span style="color: #f8f8f2">)[i])</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">public_key[i])</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(public_key))]))</span>
<span style="color: #75715e"># (NOTE: in this implementation public key is not permutated)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">gen_keypair</span><span style="color: #f8f8f2">(pt_len):</span>
	<span style="color: #75715e"># Generating Private Key:</span>
	<span style="color: #75715e"># Generating random superincreasing set w</span>
	<span style="color: #f8f8f2">w</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
	<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">2</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">_</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,pt_len):</span>
		<span style="color: #f8f8f2">value</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">SystemRandom()</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">randrange(s,s</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">W_RANGE)</span>
		<span style="color: #f8f8f2">w</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(value)</span>
		<span style="color: #f8f8f2">s</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">value</span>
	<span style="color: #75715e"># Generating q such that q &gt; sum</span>
	<span style="color: #f8f8f2">q</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">SystemRandom()</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">randrange(s,s</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">Q_RANGE)</span>
	<span style="color: #75715e"># Generating r such that r and q are coprime</span>
	<span style="color: #66d9ef">while</span> <span style="color: #66d9ef">True</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">r</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">SystemRandom()</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">randrange(</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">,q)</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">egcd(r,q)[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">:</span>
			<span style="color: #66d9ef">break</span>
	<span style="color: #f8f8f2">private_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(w,q,r)</span>
	<span style="color: #75715e">#Calculating Public Key:</span>
	<span style="color: #f8f8f2">public_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[(n</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">r)</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">q</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">w]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(public_key,</span> <span style="color: #f8f8f2">private_key)</span>
<span style="color: #75715e"># Encryption related functions - end</span>
<span style="color: #75715e"># Auxiliary functions for gcd and modulo inverse calculations</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">egcd</span><span style="color: #f8f8f2">(a,b):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">a</span> <span style="color: #f92672">==</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(b,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">g,y,x</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">egcd(b</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">a,a)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(g,x</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">(b</span><span style="color: #f92672">//</span><span style="color: #f8f8f2">a)</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">y,y)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">modinverse</span><span style="color: #f8f8f2">(a,m):</span>
	<span style="color: #f8f8f2">g,x,y</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">egcd(a,m)</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">g</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">(</span><span style="color: #e6db74">&#39;Something went wrong, modular inverse does not exist&#39;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">x</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">m</span>
<span style="color: #75715e"># Decryption related functions - start</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">verify_privatekey</span><span style="color: #f8f8f2">(private_key):</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">egcd(private_key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">],private_key[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">])[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error: q and r are not coprime!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
		<span style="color: #66d9ef">return</span> <span style="color: #66d9ef">False</span>
	<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])):</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">][i]</span> <span style="color: #f92672">&lt;=</span> <span style="color: #f8f8f2">sum:</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error: w is not a superincreasing sequence!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
			<span style="color: #66d9ef">return</span> <span style="color: #66d9ef">False</span>
		<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">][i]</span>
	<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">sum</span> <span style="color: #f92672">&gt;=</span> <span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error: q is not greater than the sum of all elements of w!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
		<span style="color: #66d9ef">return</span> <span style="color: #66d9ef">False</span>
	<span style="color: #66d9ef">return</span> <span style="color: #66d9ef">True</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">decrypt</span><span style="color: #f8f8f2">(ct,private_key):</span>
	<span style="color: #f8f8f2">s</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(ct</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">modinverse(private_key[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">],private_key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span><span style="color: #f92672">%</span><span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span>
	<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(len(private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">):</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">][i]</span> <span style="color: #f92672">&lt;=</span> <span style="color: #f8f8f2">s:</span>
			<span style="color: #f8f8f2">s</span> <span style="color: #f92672">-=</span> <span style="color: #f8f8f2">private_key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">][i]</span>
			<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">+=</span> <span style="color: #e6db74">&quot;1&quot;</span>
		<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">+=</span> <span style="color: #e6db74">&quot;0&quot;</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(hex((int(pt[::</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode())</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode()</span>
<span style="color: #75715e"># Auxiliary functions for vector operations</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">vsum</span><span style="color: #f8f8f2">(u,v):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(v)):</span>
			<span style="color: #f8f8f2">ret</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(v[i]</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">u[i])</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>
	<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error in vector sum calculation!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">scalar_product</span><span style="color: #f8f8f2">(n,v):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(v)):</span>
			<span style="color: #f8f8f2">ret</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(n</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">v[i])</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>
	<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error in vector scalar product calculation!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">dot_product</span><span style="color: #f8f8f2">(u,v):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(v)):</span>
			<span style="color: #f8f8f2">ret</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">v[i]</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">u[i]</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">ret</span>
	<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error in vector dot product calculation!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #75715e"># Cryptanalysis related functions</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">GramSchmidt</span><span style="color: #f8f8f2">(M):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">orthG</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[M[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]]</span>
		<span style="color: #f8f8f2">projection_coefficients</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{}</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,len(M)):</span>
			<span style="color: #f8f8f2">orthG</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(M[j])</span>
			<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,j):</span>
				<span style="color: #f8f8f2">projection_coefficients[str(i)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">str(j)]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(dot_product(orthG[i],M[j]))</span><span style="color: #f92672">/</span><span style="color: #f8f8f2">(dot_product(orthG[i],orthG[i]))</span>
				<span style="color: #f8f8f2">orthG[j]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">vsum(orthG[j],scalar_product(</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">projection_coefficients[str(i)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">str(j)],orthG[i]))</span>
		<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(orthG,projection_coefficients)</span>
	<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error in Gram-Schmidt orthogonalization process!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">LLL</span><span style="color: #f8f8f2">(M,d):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #66d9ef">while</span> <span style="color: #66d9ef">True</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">GSoG,</span> <span style="color: #f8f8f2">GSpc</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">GramSchmidt(M)</span>
			<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,len(M)):</span>
				<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(j</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">):</span>
					<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">abs(GSpc[str(i)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">str(j)])</span> <span style="color: #f92672">&gt;</span> <span style="color: #ae81ff">1</span><span style="color: #f92672">/</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:</span>
						<span style="color: #f8f8f2">M[j]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">vsum(M[j],scalar_product(</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">round(GSpc[str(i)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">str(j)]),M[i]))</span>
			<span style="color: #f8f8f2">GSoG,</span> <span style="color: #f8f8f2">GSpc</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">GramSchmidt(M)</span>
			<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(M)</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">):</span>
					<span style="color: #f8f8f2">tmp0</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">vsum(GSoG[j</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">],scalar_product(GSpc[str(j)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">str(j</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">)],GSoG[j]))</span>
					<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">dot_product(tmp0,tmp0)</span> <span style="color: #f92672">&lt;</span> <span style="color: #f8f8f2">d</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">(dot_product(GSoG[j],GSoG[j])):</span>
						<span style="color: #f8f8f2">tmp1</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">M[j]</span>
						<span style="color: #f8f8f2">M[j]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">M[j</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span>
						<span style="color: #f8f8f2">M[j</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">tmp1</span>
						<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">()</span>
				<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">M</span>
			<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">continue</span>
	<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Error in LLL reduction calculations!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">break_cipher</span><span style="color: #f8f8f2">(ct,public_key):</span>
	<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
		<span style="color: #75715e">#Converting the knapsack problem into a lattice problem</span>
		<span style="color: #75715e">#Initializing and setting up the matrix M</span>
		<span style="color: #f8f8f2">M</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[[</span><span style="color: #ae81ff">1</span> <span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">i</span><span style="color: #f92672">==</span><span style="color: #f8f8f2">j</span> <span style="color: #66d9ef">else</span> <span style="color: #ae81ff">0</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(public_key))]</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">j</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(public_key))]</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(public_key)):</span>
			<span style="color: #f8f8f2">M[i]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(public_key[i])</span>
		<span style="color: #f8f8f2">M</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append([</span><span style="color: #ae81ff">0</span> <span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">_</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(public_key))])</span>
		<span style="color: #f8f8f2">M[len(public_key)]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">ct)</span>
		<span style="color: #75715e">#Find short vectors in the lattice spanned by the columns of M</span>
		<span style="color: #f8f8f2">short_vectors</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">LLL(M,</span><span style="color: #ae81ff">0.99</span><span style="color: #f8f8f2">)</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Short vectors found &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(short_vectors))</span>
		<span style="color: #f8f8f2">flag</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
		<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">vector</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">short_vectors:</span>
			<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">cur</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;&quot;</span>
				<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">vector:</span>
					<span style="color: #f8f8f2">cur</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">str(n)</span>
					<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">1</span> <span style="color: #f92672">and</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
						<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">()</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Possible plaitext found &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">binascii</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">unhexlify(hex(int(cur[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">))[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:]</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">encode())</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">decode()</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span> <span style="color: #f8f8f2">)</span>
				<span style="color: #f8f8f2">flag</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">1</span>
			<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">continue</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f92672">not</span> <span style="color: #f8f8f2">flag:</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">No possible plaintexts found using LLL reduction!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Failed to break Merkle-Hellman knapsack encryption for desired ciphertext!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #75715e"># Decryption related functions - end</span>
<span style="color: #75715e"># The Main Function handles user input, menu conditions and the retrieval of information from provided text files</span>
<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">while</span> <span style="color: #66d9ef">True</span><span style="color: #f8f8f2">:</span>
		<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(banner)</span>
		<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;1) Encrypt</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">2) Decrypt</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">3) Generate Key Pair</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">4) Exit&quot;</span><span style="color: #f8f8f2">)</span>
			<span style="color: #f8f8f2">op</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;&gt; &quot;</span><span style="color: #f8f8f2">))</span>
		<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Input Error!&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #75715e"># Main menu option 1</span>
		<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">op</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;1&quot;</span><span style="color: #f8f8f2">:</span>
			<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;Plaintext to encrypt &gt; &quot;</span><span style="color: #f8f8f2">))</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Public key:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">1) Use your own key</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">2) Have key files generated for you&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #f8f8f2">op1</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;&gt; &quot;</span><span style="color: #f8f8f2">))</span>
			<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Input Error!&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">continue</span>
<span style="color: #75715e"># Encrypt menu option 1</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">op1</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;1&quot;</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">pub_file</span> <span style="color: #f92672">=</span>  <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;Enter the name of your public key file(file should have one number per line)</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&gt; &quot;</span><span style="color: #f8f8f2">))</span>
					<span style="color: #f8f8f2">public_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
					<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(pub_file,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">f:</span>
						<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">f:</span>
							<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">&lt;=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
								<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">()</span>
							<span style="color: #f8f8f2">public_key</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
				<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Invalid key error!&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">continue</span>
				<span style="color: #66d9ef">if</span> <span style="color: #f92672">not</span> <span style="color: #f8f8f2">verify_publickey(pt,public_key):</span>
					<span style="color: #f8f8f2">print(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid key error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">continue</span>
<span style="color: #75715e"># Encrypt menu option 2</span>
			<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">op1</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;2&quot;</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gen_keypair(len(pt)</span><span style="color: #f92672">*</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Key pair generated to encrypt your plaintext:</span><span style="color: #ae81ff">\n\n</span><span style="color: #e6db74">Public Key &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n\n</span><span style="color: #e6db74">Private Key(w,q,r) &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
					<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;publickey.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;w&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">pub:</span>
						<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]:</span>
							<span style="color: #f8f8f2">pub</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(n)</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;privatekey.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;w&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">prv:</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;w:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]:</span>
							<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(n)</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;q:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;r:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">public_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Public and Private keys have been saved to &#39;publickey.txt&#39; and &#39;privatekey.txt&#39; respectively.</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Input Error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
			<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid option!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">continue</span>
			<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">encrypt(pt,public_key)</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Ciphertext &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">ct</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span> 
<span style="color: #75715e"># Main menu option 2</span>
		<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">op</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;2&quot;</span><span style="color: #f8f8f2">:</span>
			<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">ct</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">int(input(</span><span style="color: #e6db74">&quot;Ciphertext to decrypt (in decimal) &gt; &quot;</span><span style="color: #f8f8f2">))</span>
			<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Input error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">continue</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Private key:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">1) Use your own key</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">2) Break Cipher (no private key required)&quot;</span><span style="color: #f8f8f2">)</span>
			<span style="color: #f8f8f2">op2</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;&gt; &quot;</span><span style="color: #f8f8f2">))</span>
<span style="color: #75715e"># Decrypt menu option 1</span>
			<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">op2</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;1&quot;</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">prv_file</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Enter the name of a private key file:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&gt; &quot;</span><span style="color: #f8f8f2">))</span>
					<span style="color: #f8f8f2">values</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
					<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(prv_file,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">prv:</span>
						<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">prv:</span>
							<span style="color: #66d9ef">if</span> <span style="color: #e6db74">&quot;w:&quot;</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">or</span> <span style="color: #e6db74">&quot;q:&quot;</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">or</span> <span style="color: #e6db74">&quot;r:&quot;</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">line:</span>
								<span style="color: #66d9ef">continue</span>
							<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">&lt;=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
								<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">()</span>
							<span style="color: #f8f8f2">values</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
					<span style="color: #f8f8f2">w</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">values[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">]</span>
					<span style="color: #f8f8f2">q</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">values[</span><span style="color: #f92672">-</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>
					<span style="color: #f8f8f2">r</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">values[</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">:][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>
					<span style="color: #f8f8f2">private_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(w,q,r)</span>
					<span style="color: #66d9ef">if</span> <span style="color: #f92672">not</span> <span style="color: #f8f8f2">verify_privatekey(private_key):</span>
						<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid key error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
						<span style="color: #66d9ef">continue</span>
					<span style="color: #f8f8f2">pt</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">decrypt(ct,private_key)</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Plaintext &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">pt</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid key error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">continue</span>
<span style="color: #75715e"># Decrypt menu option 2</span>
			<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">op2</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;2&quot;</span><span style="color: #f8f8f2">:</span>
				<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">pub_file</span> <span style="color: #f92672">=</span>  <span style="color: #f8f8f2">str(input(</span><span style="color: #e6db74">&quot;Enter the name of a public key file</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&gt; &quot;</span><span style="color: #f8f8f2">))</span>
					<span style="color: #f8f8f2">public_key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[]</span>
					<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(pub_file,</span><span style="color: #e6db74">&quot;r&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">pub:</span>
						<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">line</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">pub:</span>
							<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">&lt;=</span> <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:</span>
								<span style="color: #66d9ef">raise</span> <span style="color: #a6e22e">Exception</span><span style="color: #f8f8f2">()</span>
							<span style="color: #f8f8f2">public_key</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">append(int(line[:</span><span style="color: #f92672">-</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
				<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
					<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid key error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">continue</span>
				<span style="color: #f8f8f2">break_cipher(ct,public_key)</span>
			<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid option!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">continue</span>
<span style="color: #75715e"># Main menu option 3</span>
		<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">op</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;3&quot;</span><span style="color: #f8f8f2">:</span>
			<span style="color: #66d9ef">try</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">size</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">int(input(</span><span style="color: #e6db74">&quot;Enter key size(in bytes):</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&gt; &quot;</span><span style="color: #f8f8f2">))</span>
				<span style="color: #f8f8f2">key</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gen_keypair(size</span><span style="color: #f92672">*</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Key pair generated to encrypt your plaintext:</span><span style="color: #ae81ff">\n\n</span><span style="color: #e6db74">Public Key &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n\n</span><span style="color: #e6db74">Private Key(w,q,r) &gt; &quot;</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]))</span>
				<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;publickey.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;w&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">pub:</span>
					<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">key[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]:</span>
						<span style="color: #f8f8f2">pub</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(n)</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;privatekey.txt&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;w&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">prv:</span>
					<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;w:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]:</span>
						<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(n)</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;q:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(</span><span style="color: #e6db74">&quot;r:</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
					<span style="color: #f8f8f2">prv</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">write(str(key[</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">][</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">])</span> <span style="color: #f92672">+</span> <span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Public and Private keys have been saved to &#39;publickey.txt&#39; and &#39;privatekey.txt&#39; respectively.</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
			<span style="color: #66d9ef">except</span><span style="color: #f8f8f2">:</span>
				<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Input error!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
				<span style="color: #66d9ef">continue</span>
<span style="color: #75715e"># Main menu option 4</span>
		<span style="color: #66d9ef">elif</span> <span style="color: #f8f8f2">op</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;4&quot;</span><span style="color: #f8f8f2">:</span>
			<span style="color: #66d9ef">return</span> <span style="color: #ae81ff">0</span>
		<span style="color: #66d9ef">else</span><span style="color: #f8f8f2">:</span>
			<span style="color: #f8f8f2">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">Invalid option!</span><span style="color: #ae81ff">\n</span><span style="color: #e6db74">&quot;</span><span style="color: #f8f8f2">)</span>
<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
