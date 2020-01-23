---
layout: post
title: "Midnight Sun CTF 2019 Quals Write-Up: open-gyckel-crypto"
description: "Fun RSA/Math challenge"
---
   

 
  <body>

    
    <!-- Page Content -->
    <div class="container">

      <div class="row">

        <!-- Blog Entries Column -->
        <div class="col-md-8">

          <h1 class="my-4"><font color="black">Midnight Sun CTF 2019 Quals Write-up</font></h1>

          <!-- Image -->
          <img class="card-img-top" src="../../../assets/images/gyckel.png" alt="Card image cap">
          <div class="card mb-4">
            <div class="card-body">
              <p></p>
              <p class="lead"> For this challenge we're given the following code:
          	</p>
       		   <div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #66d9ef">while</span> <span style="color: #f8f8f2">True:</span>
    <span style="color: #f8f8f2">p</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">next_prime(random</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">randint(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">500</span><span style="color: #f8f8f2">))</span>
    <span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">len(str(p))</span> <span style="color: #f92672">!=</span> <span style="color: #ae81ff">500</span><span style="color: #f8f8f2">:</span>
        <span style="color: #66d9ef">continue</span>
    <span style="color: #f8f8f2">q</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">Integer(int(str(p)[</span><span style="color: #ae81ff">250</span><span style="color: #f8f8f2">:]</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">str(p)[:</span><span style="color: #ae81ff">250</span><span style="color: #f8f8f2">]))</span>
    <span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">q</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">is_prime():</span>
        <span style="color: #66d9ef">break</span>

<span style="color: #f92672">&gt;&gt;</span> <span style="color: #f8f8f2">p</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">q</span>
<span style="color: #ae81ff">6146024643941503757217715363256725297474582575057128830681803952150464985329239705861504172069973746764596350359462277397739134788481500502387716062571912861345331755396960400668616401300689786263797654804338789112750913548642482662809784602704174564885963722422299918304645125966515910080631257020529794610856299507980828520629245187681653190311198219403188372517508164871722474627810848320169613689716990022730088459821267951447201867517626158744944551445617408339432658443496118067189012595726036261168251749186085493288311314941584653172141498507582033165337666796171940245572657593635107816849481870784366174740265906662098222589242955869775789843661127411493630943226776741646463845546396213149027737171200372484413863565567390083316799725434855960709541328144058411807356607316377373917707720258565704707770352508576366053160404360862976120784192082599228536166245480722359263166146184992593735550019325337524138545418186493193366973466749752806880403086988489013389009843734224502284325825989</span>
<span style="color: #f92672">&gt;&gt;</span> <span style="color: #f8f8f2">pow(m,</span> <span style="color: #ae81ff">65537</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">p</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">q)</span>
<span style="color: #ae81ff">3572030904528013180691184031825875018560018830056027446538585108046374607199842488138228426133620939067295245642162497675548656988031367698701161407333098336631469820625758165691216722102954230039803062571915807926805842311530808555825502457067483266045370081698397234434007948071948000301674260889742505705689105049976374758307610890478956315615270346544731420764623411884522772647227485422185741972880247913540403503772495257290866993158120540920089734332219140638231258380844037266185237491107152677366121632644100162619601924591268704611229987050199163281293994502948372872259033482851597923104208041748275169138684724529347356731689014177146308752441720676090362823472528200449780703866597108548404590800249980122989260948630061847682889941399385098680402067366390334436739269305750501804725143228482932118740926602413362231953728010397307348540059759689560081517028515279382023371274623802620886821099991568528927696544505357451279263250695311793770159474896431625763008081110926072287874375257</span>
</pre></div>
          <p></p>
          <p>First let's make sure we understand what's going on. This was a pretty interesting RSA math challenge were <b>p</b> is a random prime of size 500 digits. Here's were things get interesting though, let's say L represents the 250 leftmost digits of <b>p</b>(ordered from left to right) and R the 250 rightmost digits of <b>p</b> (ordered from left to right), <b>q</b> is equal to L concatenated with R. So the code splits p's digits in half swaps the left half with the right half and <b>q</b> becomes the result of this operation(note the code makes sure both are primes).</p>
          <p>Knowing this we can write <b>p</b> using L and P as:</p>
          <ul><li><b>p = L * 10<sup>(len(p)/2)</sup> + R = L * 10<sup>250</sup> + R</b></li></ul>
          <p>Likewise, since we have an established relation between <b>p</b> and <b>q</b> we can write <b>q</b> as:</p>
          <ul><li><b>q = R * 10<sup>(len(p)/2)</sup> + L = R * 10<sup>250</sup> + L</b></li></ul>
          <p>And now we can use some math to write <b>n</b> as:</p>
          <ul>
            <li><b>n = p*q = (L * 10<sup>250</sup> + R) * (R * 10<sup>250</sup> + L)</b></li>
            <li><b>n = L * 10<sup>250</sup> * R + L<sup>2</sup> * 10<sup>250</sup> + R<sup>2</sup> * 10<sup>250</sup> + L*R</b></li>
            <li><b>n = LR * 10<sup>500</sup> + (L<sup>2</sup> + R<sup>2</sup>)*10<sup>250</sup> + L*R</b></li>
          </ul>
          <p>From here we can conclude that the 250 leftmost digits of L*R must corresponde to the 250 leftmost digits of n (minus a possible carry, wich can be easily bruteforced if present) and the 250 rightmost digits of L*R must correspond to the 250 rightmost digits of n. The following python line might ilustrate better how we can calculate L*R (also keep in mind that n is 1000 digits long):</p>
          <div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">LR</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(int(str(n)[:</span><span style="color: #ae81ff">250</span><span style="color: #f8f8f2">])</span><span style="color: #f92672">-</span><span style="color: #ae81ff">carry</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">*</span><span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">250</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">int(str(n)[</span><span style="color: #ae81ff">750</span><span style="color: #f8f8f2">:])</span>
</pre></div>
          <p></p>
          <p>Now that we have a way to calculate L*R we can write an equation to calculate (L<sup>2</sup>+R<sup>2</sup>) based on our previous result:</p>
          <ul>
            <li><b>  n = L*R * 10<sup>500</sup> + (L<sup>2</sup> + R<sup>2</sup>)*10<sup>250</sup> + L*R</b></li>
            <li><b> (=) n - L*R * 10<sup>500</sup> = (L<sup>2</sup> + R<sup>2</sup>)*10<sup>250</sup> + L*R</b></li>
            <li><b> (=) n - L*R * 10<sup>500</sup> - L*R = (L<sup>2</sup> + R<sup>2</sup>)*10<sup>250</sup></b></li>
            <li><b> (=) (n - L*R * 10<sup>500</sup> - L*R)/10<sup>250</sup> = L<sup>2</sup> + R<sup>2</sup></b></li>
            <li><b> (=) L<sup>2</sup> + R<sup>2</sup> = (n - L*R * 10<sup>500</sup> - L*R)/10<sup>250</sup></b></li>
          </ul>
          <p>Here I admittedly got stuck for quite a while. I was having trouble figuring out how to get the correct values for p and q from here. Playing around with some math, I wrote, among other things, phi(n) as: </p>
          <ul>
            <li><b>phi(n) = (p-1)*(q-1)</b></li>
            <li><b>phi(n) = p*q - p - q + 1</b></li>
            <li><b>phi(n) = p*q - (p + q) + 1</b></li>
          </ul>
          <p>But I didn't immediately see how we can use this to solve the challenge. Then it hit me. We can write <b>p + q</b> as:</p>
          <ul>
            <li><b>p + q = (L * 10<sup>250</sup> + R) + (R * 10<sup>250</sup> + L)</b></li>
            <li><b>p + q = (L + R) * 10<sup>250</sup> + L + R</b></li>
          </ul>


            <p>We can then write (L + R)<sup>2</sup> as:</p>
          <ul>
            <li><b>(L + R)<sup>2</sup> = L<sup>2</sup>+R<sup>2</sup>+2*L*R</b></li>
          </ul>
          <p>And since we can calculate both <b>L*R</b> and <b>L<sup>2</sup>+R<sup>2</sup></b> we can calculate <b>(L+R)<sup>2</sup></b> and thus calculate <b>(L+R)</b> since <b>(L+R) = sqrt((L+R)<sup>2</sup>)</b>. Knowing <b>L + R</b> we can calculate <b>p + q</b> and thus calculate<b>phi(n)</b> without even knowing the exact values of <b>p</b> and <b>q</b></p>
          <p>Solver:</p>
          <div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f92672">import</span> <span style="color: #f8f8f2">codecs</span>
<span style="color: #f92672">import</span> <span style="color: #f8f8f2">gmpy</span>

<span style="color: #f8f8f2">decode_hex</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">codecs</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">getdecoder(</span><span style="color: #e6db74">&quot;hex_codec&quot;</span><span style="color: #f8f8f2">)</span>

<span style="color: #f8f8f2">n</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">6146024643941503757217715363256725297474582575057128830681803952150464985329239705861504172069973746764596350359462277397739134788481500502387716062571912861345331755396960400668616401300689786263797654804338789112750913548642482662809784602704174564885963722422299918304645125966515910080631257020529794610856299507980828520629245187681653190311198219403188372517508164871722474627810848320169613689716990022730088459821267951447201867517626158744944551445617408339432658443496118067189012595726036261168251749186085493288311314941584653172141498507582033165337666796171940245572657593635107816849481870784366174740265906662098222589242955869775789843661127411493630943226776741646463845546396213149027737171200372484413863565567390083316799725434855960709541328144058411807356607316377373917707720258565704707770352508576366053160404360862976120784192082599228536166245480722359263166146184992593735550019325337524138545418186493193366973466749752806880403086988489013389009843734224502284325825989</span>
<span style="color: #f8f8f2">c</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">3572030904528013180691184031825875018560018830056027446538585108046374607199842488138228426133620939067295245642162497675548656988031367698701161407333098336631469820625758165691216722102954230039803062571915807926805842311530808555825502457067483266045370081698397234434007948071948000301674260889742505705689105049976374758307610890478956315615270346544731420764623411884522772647227485422185741972880247913540403503772495257290866993158120540920089734332219140638231258380844037266185237491107152677366121632644100162619601924591268704611229987050199163281293994502948372872259033482851597923104208041748275169138684724529347356731689014177146308752441720676090362823472528200449780703866597108548404590800249980122989260948630061847682889941399385098680402067366390334436739269305750501804725143228482932118740926602413362231953728010397307348540059759689560081517028515279382023371274623802620886821099991568528927696544505357451279263250695311793770159474896431625763008081110926072287874375257</span> 
<span style="color: #f8f8f2">e</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">65537</span>


<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">carry</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">1337</span><span style="color: #f8f8f2">):</span>
  <span style="color: #f8f8f2">LR</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(int(str(n)[:</span><span style="color: #ae81ff">250</span><span style="color: #f8f8f2">])</span><span style="color: #f92672">-</span><span style="color: #f8f8f2">carry)</span><span style="color: #f92672">*</span><span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">250</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">int(str(n)[</span><span style="color: #ae81ff">750</span><span style="color: #f8f8f2">:])</span>

  <span style="color: #f8f8f2">L2_plus_R2</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(n</span> <span style="color: #f92672">-</span> <span style="color: #f8f8f2">LR</span> <span style="color: #f92672">*</span> <span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">500</span>  <span style="color: #f92672">-</span> <span style="color: #f8f8f2">LR)</span> <span style="color: #f92672">//</span> <span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">250</span>

  <span style="color: #66d9ef">if</span><span style="color: #f8f8f2">((LR</span><span style="color: #f92672">*</span><span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">500</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">L2_plus_R2</span><span style="color: #f92672">*</span><span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">250</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">LR)</span> <span style="color: #f92672">==</span> <span style="color: #f8f8f2">n):</span>
    <span style="color: #66d9ef">break</span>

<span style="color: #f8f8f2">L_plus_R</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gmpy</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">root(L2_plus_R2</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">2</span><span style="color: #f92672">*</span><span style="color: #f8f8f2">LR,</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>

<span style="color: #f8f8f2">phin</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">n</span> <span style="color: #f92672">-</span> <span style="color: #f8f8f2">(L_plus_R</span><span style="color: #f92672">*</span><span style="color: #ae81ff">10</span><span style="color: #f92672">**</span><span style="color: #ae81ff">250</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">L_plus_R)</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">1</span>

<span style="color: #f8f8f2">d</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">gmpy</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">invert(e,phin)</span>

<span style="color: #f8f8f2">m</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">pow(c,d,n)</span>

<span style="color: #f8f8f2">flag</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">decode_hex(hex(m)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:])[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>

<span style="color: #66d9ef">print</span><span style="color: #f8f8f2">(flag)</span>
</pre></div>
          <p></p>
          <p><b>Flag: midnight{w3ll_wh47_d0_y0u_kn0w_7h15_15_4c7u4lly_7h3_w0rld5_l0n6357_fl46_4nd_y0u_f0und_17_</b></p>
          <b><p>50_y0u_5h0uld_b3_pr0ud_0f_y0ur53lf_50_uhmm_60_pr1n7_7h15_0n_4_75h1r7_0r_50m37h1n6_4nd</p></b><b>_4m4z3_7h3_p30pl3_4r0und_y0u}</b>






            </div>
          </div>


        </div>

        

      </div>
      <!-- /.row -->

    </div>
    <!-- /.container -->




  </body>
 


