---
layout: post
title: "Reverse Engineering Pokemon NDS save files"
description: "[project] Reverse Engineering 4th generation Pokemon NDS games' save files and writting a save file editor"
categories: "Project"
---

<style type="text/css">

.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}


table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #000;
  text-align: center;
  padding: 8px;
}

tr:nth-child(1) {
  background-color: #ddd;
}

</style>

<title>Reverse Engineering Pokemon NDS save files - LostMyPlaintext</title>
<p>Partially inspired by [<a href="https://www.youtube.com/watch?v=VVbRe7wr3G4">LiveOverflow's video series</a>] on Pokemon Red I decided to revisit a few classics from my own childhood: the 4th generation Pokemon main game titles. Mainly I really wanted to replay these games using whatever Pokemon I wanted and not just the ones availiable at certain parts of each game so from the beginning my main goal was to find a way to edit a Pokemon's species. I'll be mostly focusing on [<a href="https://bulbapedia.bulbagarden.net/wiki/Pok%C3%A9mon_Platinum_Version">Pokemon Platinum version</a>] but most of this information applies to all main games from the same generation (only offsets differ).</p> 
<hr style="border-top:1px solid #28323C;">
<p></p>
<p><h3>Content:</h3></p>
<ul>
	<li><a href="#Starting Point">Starting Point</a></li>
	<li><a href="#Save file memory blocks and Bypassing CRC checksum">Save file memory blocks and Bypassing CRC checksum</a></li>
	<li><a href="#The Pokemon data structure">The Pokemon data structure</a></li>
	<li><a href="#Conclusion and future work">Conclusion and future work</a></li>
</ul>
<p><a name="Starting Point"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p><h3>Starting Point</h3></p>
<p></p>
<p>Before doing anything remotely exciting I obviously needed to be able to run the games on my computer. I decided to go with the <a href="http://desmume.org/"><b>DeSmuME</b></a> emulator as it was the easiest to get to run properly on my OS of choice. (Disclaimer: I do own physical copies of these games but if you're going to download them either way at least make sure you get them from a reputable source).</p>
<p></p>
<p></p>
<p><b>Getting Started With Reverse Engineering:</b><br>First I started two new save files, naming my character "AAAAAAA" in the first one and "BBBBBBB" in the second</p>
<p></p>
<img class="center" src="/assets/images/AABB.png">
<p></p>
<p>Then opened both files in a hexeditor (actually I used vimdiff and the xxd command but you get the idea) and checked for differences</p>
<p></p>
<img class="center" src="/assets/images/diff.png">
<p></p>
<p>In the "A" file we find the sequence of bytes "2b01" seven times in a row and in the "B" file we find the sequence of bytes "2c01" seven times in a row. Considering that our character's name is seven characters long and that the number 2c comes after the number 2b, it seems highly likely that we found the player name within the save file. But if that's the case why are characters encoded in two bytes? What is that "01" for? I later found out through trial and error that it's a sort of region encoding. For example if you were to change "01" to "00" you'd get japanese characters instead of the latin alphabet. Nice so if we were to change all the "2b"s to "2d"s in the first file, our name in game would change to "CCCCCCC" once we load the save file... right...? Well...</p>
<p></p>
<img class="center" src="/assets/images/savefiledeleted.png">
<p></p>
<p>... not exactly.</p>
<p><a name="Save file memory blocks and Bypassing CRC checksum"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p><h3>Save file memory blocks and Bypassing CRC checksum</h3></p>
<p></p>
<p><b>Save file memory blocks:</b><br>
	Before we adress this issue a quick explanation of how information is laid out within the file. Save files are divided into two pairs of blocks, small blocks and big blocks (here you can see at what values they start and end): </p> 

<img class="center" src="/assets/images/blocks.png">
<p> With that said the data we want to mess with (player information, party pokemon, items, etc.) is all stored in the small blocks so we'll be focusing on those. We'll be referencing this <a href="https://bulbapedia.bulbagarden.net/wiki/Save_data_structure_in_Generation_IV"><b>Bulbapedia* page</b></a> a lot from this point onward as it gives a lot of usefull information like the offsets for things we want to edit.</p>
<p>(Note: it seems the first time you save the game all data stored in small blocks gets saved only to the second block. The second time you save the game it gets saved to the first block and your previous save remains in the second block. From there on out your most recent save always gets written to the fisrt block and the previous save to the second block. So it appears the game stores your last two saves at all times although I still need to fully confirm this.)</p>
<p></p>

<p><b>Bypassing CRC checksum:</b><br>
	Turns out the game uses an error detection algorithm to assure data integrity, causing our save file to become corrupted if we try to edit stuff(from the same<a href="https://bulbapedia.bulbagarden.net/wiki/Save_data_structure_in_Generation_IV#Checksum"><b> Bulbapedia page</b></a> we know the algorithm used is <a href="https://en.wikipedia.org/wiki/Cyclic_redundancy_check"><b>CRC-16-CCITT</b></a>). Basically, it calculates a checksum value based on part of the contents of the save file (in this case it takes all the bytes in the small blocks except for the block's footer: it's last 20 bytes) and checks if said value is consistent. Good news is these kinds of algorithms aren't design to stop attackers. The checksum value is also stored in the save file so we can just:<br>
	<ul>
		<li>Implement the algorithm</li> 
		<li>Make the changes we want to the save file</li> 
		<li>Run the algorithm with the modified file to get the updated checksum value</li> 
		<li>Switch the previous checksum value with the new one</li> 
	</ul>
	According to Bulbapedia the checksum bytes should be at 0x0CF2A-0x0CF2B for the first small block and 0x4CF2A-0x4CF2B for the second. We can confirm there are two different pairs bytes in our "A" and "B" files at addresses 0x4CF2A-0x4CF2B (again the first block is still unused because we only saved once):</p>
<p></p>
<img class="center" src="/assets/images/checksum.png">
<p></p>
<p>Let's make sure these values correspond to the checksum by implementing CRC-16-CCITT. There are <a href="https://en.wikipedia.org/wiki/Computation_of_CRC"><b>several ways</b></a> we can go about doing this, I opted for using a pre computed table (which I later realized is not necessarily optimal but for now let's go with it). Let's have the code print the values it calculates in hex (also note the values are stored in little-endian):</p>
<p></p>

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">small_block_2</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0x40000</span>
<span style="color: #f8f8f2">small_block_footer_offset</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xcf18</span>


<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">checksum</span><span style="color: #f8f8f2">(data):</span>
	<span style="color: #f8f8f2">table</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">[</span><span style="color: #ae81ff">0x0000</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1021</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2042</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3063</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4084</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x50A5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x60C6</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x70E7</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x8108</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9129</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA14A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB16B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC18C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD1AD</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE1CE</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF1EF</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x1231</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0210</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3273</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2252</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x52B5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4294</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x72F7</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x62D6</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x9339</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8318</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB37B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA35A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD3BD</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC39C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF3FF</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE3DE</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x2462</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3443</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0420</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1401</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x64E6</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x74C7</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x44A4</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5485</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xA56A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB54B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8528</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9509</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE5EE</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF5CF</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC5AC</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD58D</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x3653</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2672</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1611</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0630</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x76D7</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x66F6</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5695</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x46B4</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xB75B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA77A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9719</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8738</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF7DF</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE7FE</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD79D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC7BC</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x48C4</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x58E5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6886</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x78A7</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0840</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1861</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2802</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3823</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xC9CC</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD9ED</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE98E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF9AF</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8948</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9969</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA90A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB92B</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x5AF5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4AD4</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7AB7</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6A96</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1A71</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0A50</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3A33</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2A12</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xDBFD</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xCBDC</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xFBBF</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xEB9E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9B79</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8B58</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBB3B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xAB1A</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x6CA6</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7C87</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4CE4</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5CC5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2C22</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3C03</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0C60</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1C41</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xEDAE</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xFD8F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xCDEC</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xDDCD</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xAD2A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBD0B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8D68</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9D49</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x7E97</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6EB6</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5ED5</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4EF4</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3E13</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2E32</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1E51</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0E70</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xFF9F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xEFBE</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xDFDD</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xCFFC</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBF1B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xAF3A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9F59</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8F78</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x9188</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x81A9</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB1CA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA1EB</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD10C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC12D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF14E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE16F</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x1080</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x00A1</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x30C2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x20E3</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5004</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4025</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7046</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6067</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x83B9</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9398</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA3FB</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB3DA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC33D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD31C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE37F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF35E</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x02B1</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1290</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x22F3</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x32D2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4235</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5214</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6277</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7256</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xB5EA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA5CB</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x95A8</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8589</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF56E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE54F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD52C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC50D</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x34E2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x24C3</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x14A0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0481</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7466</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6447</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5424</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4405</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xA7DB</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB7FA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8799</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x97B8</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE75F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF77E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC71D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xD73C</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x26D3</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x36F2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0691</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x16B0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6657</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7676</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4615</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5634</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xD94C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xC96D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xF90E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xE92F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x99C8</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x89E9</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xB98A</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xA9AB</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x5844</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4865</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7806</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6827</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x18C0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x08E1</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3882</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x28A3</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xCB7D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xDB5C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xEB3F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xFB1E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8BF9</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9BD8</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xABBB</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBB9A</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x4A75</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5A54</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6A37</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7A16</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0AF1</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1AD0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2AB3</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3A92</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xFD2E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xED0F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xDD6C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xCD4D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBDAA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xAD8B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9DE8</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8DC9</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x7C26</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x6C07</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5C64</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4C45</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3CA2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2C83</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1CE0</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0CC1</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0xEF1F</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xFF3E</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xCF5D</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xDF7C</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xAF9B</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0xBFBA</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x8FD9</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x9FF8</span><span style="color: #f8f8f2">,</span>
	<span style="color: #ae81ff">0x6E17</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x7E36</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x4E55</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x5E74</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x2E93</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x3EB2</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x0ED1</span><span style="color: #f8f8f2">,</span> <span style="color: #ae81ff">0x1EF0</span><span style="color: #f8f8f2">]</span>

	<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xffff</span>
	<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(</span><span style="color: #e6db74">&quot;Computing checksum...&quot;</span><span style="color: #f8f8f2">)</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(data)):</span>
		<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(sum</span> <span style="color: #f92672">&lt;&lt;</span> <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">table[data[i]</span><span style="color: #f92672">^</span><span style="color: #f8f8f2">int(bin(sum</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)]</span>
	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">(hex(int(bin(sum)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">))</span> <span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">hex(int(bin(sum</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)))</span>


<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">main</span><span style="color: #f8f8f2">():</span>
	<span style="color: #66d9ef">with</span> <span style="color: #f8f8f2">open(</span><span style="color: #e6db74">&quot;AAAAAAA.dsv&quot;</span><span style="color: #f8f8f2">,</span><span style="color: #e6db74">&quot;rb&quot;</span><span style="color: #f8f8f2">)</span> <span style="color: #66d9ef">as</span> <span style="color: #f8f8f2">f:</span>
		<span style="color: #f8f8f2">sav</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">bytearray(f</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">read())</span>
		<span style="color: #66d9ef">print</span> <span style="color: #f8f8f2">(checksum(sav[small_block_2:(small_block_2</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">small_block_footer_offset)]))</span>


<span style="color: #66d9ef">if</span> <span style="color: #f8f8f2">__name__</span> <span style="color: #f92672">==</span> <span style="color: #e6db74">&quot;__main__&quot;</span><span style="color: #f8f8f2">:</span>
	<span style="color: #f8f8f2">main()</span>
</pre></div>
<p></p>
<p>This code outputs the following:</p>
<p></p>
<img class="center" src="/assets/images/checksumoutput.png">
<p></p>
<p>The same values we find at addresses 0x4CF2A-0x4CF2B meaning we can be sure we found the checksum value. Now let's try changing all the "2b"s to "2d"s in the "A" file once more:</p>
<p></p>
<img class="center" src="/assets/images/namechanged.png">
<p></p>
<p>Then we run the same program to calculate the new checksum:</p>
<p></p>
<img class="center" src="/assets/images/newchecksum.png">
<p></p>
<p>And we edit the checksum within the file:</p>
<p></p>
<img class="center" src="/assets/images/checksumchanged.png">
<p></p>
<p>And now if we load the save file...</p>
<p></p>
<img class="center" src="/assets/images/CC.png">
<p></p>
<p>Success!</p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p><h5>*The Pokemon Encyclopedia</h5></p>
<p><a name="The Pokemon data structure"></a></p>
<hr style="border-top:1px solid #28323C;">
<p><h3>The Pokemon data structure</h3></p>
<p></p>
<p>Now that we can make changes to our save files without corrupting them let's see if we can find a way to change our Pokemon's species. The data for the up to six Pokemon in the player's party is all stored in the small blocks (each party Pokemon is 236 bytes in size). To keep things simple we'll be focusing on editing only the first Pokemon in the party.</p>
<p>From the Bulbapedia page we've been referencing thus far we know we can find the party Pokemon data from save file offsets 0xA0 to 0x627 and we can learn more about the location of specific offsets for things like the Pokemon's species id (or it's <a href="https://bulbapedia.bulbagarden.net/wiki/List_of_Pok%C3%A9mon_by_National_Pok%C3%A9dex_number"><b>national dex number</b></a>), its moves and ability from <a href="https://bulbapedia.bulbagarden.net/wiki/Save_data_structure_in_Generation_IV"><b>this page</b></a>. This is where we run into another problem though.</p>
<p>Not only are bytes 0x08 to 0xEB of the Pokemon data structure <a href="https://en.wikipedia.org/wiki/XOR_cipher"><b>xor encrypted</b></a>, this encryption uses a <a href="https://bulbapedia.bulbagarden.net/wiki/Pseudorandom_number_generation_in_Pok%C3%A9mon"><b>pseudorandom number generator (PRNG)</b></a> based on a checksum independent from the one we just bypassed. Furthermore bytes 0x08 to 0x87 are devided into four 32-byte blocks and shuffled around.</p>
<p>Thankfully, with the resources linked so far we can learn how these things are implemented and how to find workarrounds (hopefully without too much trouble).</p>
<p>Let's start by pointing out that bytes 0x01 to 0x07 are not encrypted and contain usefull information such as the checksum value and the personality value or pv of the Pokemon (for the purposes of this post you can think of it as a value that varies from Pokemon to Pokemon).</p>
<p><b>Decrypting the Pokemon structure:</b><br>
		Let's tackle the encryption first. From Bulbapedia we know that the PRNG uses the follwing formula:<br>
		<ul>
			<li>X[n+1] = (0x41C64E6D * X[n] + 0x6073)</li>
		</ul>
		To encrypt/decrypt the Pokemon data the game uses the Pokemon specific checksum value (let's refer to it as the Pokemon checksum) as the first instance of X[n] then for the nth number generated:<br>
		<ul>
			<li>Takes the 16 upper bytes of said number</li>
			<li>Takes the nth 2-byte word from Pokemon data offsets 0x08 to 0x87</li>
			<li>And XORs them</li>
		</ul>
		Meaning we can just implement these steps and we'll have a way to encrypt as well as decrypt (since the xor cipher is <a href="https://en.wikipedia.org/wiki/Symmetric-key_algorithm"><b>symmetric</b></a>) Pokemon data blocks:   
</p>
<p></p>

<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #f8f8f2">small_block_offset</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0x40000</span>
<span style="color: #f8f8f2">lead_Pokemon_offset</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0xA0</span>

<span style="color: #66d9ef">def</span> <span style="color: #a6e22e">PRNG</span><span style="color: #f8f8f2">(data,checksum):</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">128</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">):</span>
		<span style="color: #f8f8f2">seed</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">(</span><span style="color: #ae81ff">0x41C64E6D</span> <span style="color: #f92672">*</span> <span style="color: #f8f8f2">seed)</span> <span style="color: #f92672">+</span> <span style="color: #ae81ff">0x00006073</span>
		<span style="color: #f8f8f2">data[(small_block_offset</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">lead_Pokemon_offset</span><span style="color: #f92672">+</span><span style="color: #ae81ff">0x08</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">i]</span> <span style="color: #f92672">^=</span> <span style="color: #f8f8f2">int(bin(seed</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">16</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span>
		<span style="color: #f8f8f2">data[(small_block_offset</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">lead_Pokemon_offset</span><span style="color: #f92672">+</span><span style="color: #ae81ff">0x08</span><span style="color: #f8f8f2">)</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">i</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">^=</span> <span style="color: #f8f8f2">int(bin(seed</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">24</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span>
</pre></div>

<p></p>
<p><b>Unshuffling the 32-byte blocks:</b><br>
	Like we mentioned already, Pokemon data from bytes 0x08 to 0x87 is shuffled into four different blocks (let's call these blocks A, B, C and D). First let's undestand how the blocks are shuffled. Using the following formula:<br>
	<ul>
		<li>((pv & 0x3E000) >> 0xD) % 24 (where pv is the Pokemon's personality value)</li>
	</ul>
	The game orders the blocks based on the result, using the follwing table:
</p>
<table>
  <tr>
    <th>Result</th>
    <th>Order</th>
  </tr>

  <tr>
    <td>0</td>
    <td>ABCD</td>
  </tr>

  <tr>
    <td>1</td>
    <td>ABDC</td>
  </tr>

  <tr>
    <td>2</td>
    <td>ACBD</td>
  </tr>

  <tr>
    <td>3</td>
    <td>ACDB</td>
  </tr>

  <tr>
    <td>4</td>
    <td>ADBC</td>
  </tr>

  <tr>
    <td>5</td>
    <td>ADCB</td>
  </tr>

  <tr>
    <td>6</td>
    <td>BACD</td>
  </tr>

  <tr>
    <td>7</td>
    <td>BADC</td>
  </tr>

  <tr>
    <td>8</td>
    <td>BCAD</td>
  </tr>

  <tr>
    <td>9</td>
    <td>BCDA</td>
  </tr>

  <tr>
    <td>10</td>
    <td>BDAC</td>
  </tr>

  <tr>
    <td>11</td>
    <td>BDCA</td>
  </tr>

  <tr>
    <td>12</td>
    <td>CABD</td>
  </tr>

  <tr>
    <td>13</td>
    <td>CADB</td>
  </tr>

  <tr>
    <td>14</td>
    <td>CBAD</td>
  </tr>

  <tr>
    <td>15</td>
    <td>CBDA</td>
  </tr>

  <tr>
    <td>16</td>
    <td>CDAB</td>
  </tr>

  <tr>
    <td>17</td>
    <td>CDBA</td>
  </tr>

  <tr>
    <td>18</td>
    <td>DABC</td>
  </tr>

  <tr>
    <td>19</td>
    <td>DACB</td>
  </tr>

  <tr>
    <td>20</td>
    <td>DBAC</td>
  </tr>

  <tr>
    <td>21</td>
    <td>DBCA</td>
  </tr>

  <tr>
    <td>22</td>
    <td>DCAB</td>
  </tr>

  <tr>
    <td>23</td>
    <td>DCBA</td>
  </tr>
</table>
<p></p>
<p>Knowing this as well as the personality value (stored in bytes 0x00 to 0x03 which are not encrypted) we can write a function to calculate the correct block order for the Pokemon we're trying to edit:</p>
<p></p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #66d9ef">def</span> <span style="color: #a6e22e">getBlockOffsets</span><span style="color: #f8f8f2">(pv):</span>
	<span style="color: #f8f8f2">orderTable</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">{</span>
			<span style="color: #75715e"># A offset -&gt; index 0; B offset -&gt; index 1; C offset -&gt; index 2; D offset -&gt; index 3</span>
		   	 <span style="color: #ae81ff">0</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ABCD</span>
			 <span style="color: #ae81ff">1</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ABDC</span>
			 <span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ACBD</span>
			 <span style="color: #ae81ff">3</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ACDB</span>
			 <span style="color: #ae81ff">4</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ADBC</span>
			 <span style="color: #ae81ff">5</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#ADCB</span>
			 <span style="color: #ae81ff">6</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BACD</span>
			 <span style="color: #ae81ff">7</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BADC</span>
			 <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BCAD</span>
			 <span style="color: #ae81ff">9</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BCDA</span>
			<span style="color: #ae81ff">10</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BDAC</span>
			<span style="color: #ae81ff">11</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#BDCA</span>
			<span style="color: #ae81ff">12</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CABD</span>
			<span style="color: #ae81ff">13</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CADB</span>
			<span style="color: #ae81ff">14</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CBAD</span>
			<span style="color: #ae81ff">15</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CBDA</span>
			<span style="color: #ae81ff">16</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CDAB</span>
			<span style="color: #ae81ff">17</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#CDBA</span>
			<span style="color: #ae81ff">18</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#DABC</span>
			<span style="color: #ae81ff">19</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#DACB</span>
			<span style="color: #ae81ff">20</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#DBAC</span>
			<span style="color: #ae81ff">21</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#DBCA</span>
			<span style="color: #ae81ff">22</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">],</span> <span style="color: #75715e">#DCAB</span>
			<span style="color: #ae81ff">23</span><span style="color: #f8f8f2">:[</span><span style="color: #ae81ff">96</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">64</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">32</span><span style="color: #f8f8f2">,</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">]</span>  <span style="color: #75715e">#DCBA</span>
		<span style="color: #f8f8f2">}</span>

	<span style="color: #66d9ef">return</span> <span style="color: #f8f8f2">orderTable[((pv</span> <span style="color: #f92672">&amp;</span> <span style="color: #ae81ff">0x3e000</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">0xd</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">%</span> <span style="color: #ae81ff">24</span><span style="color: #f8f8f2">]</span>
</pre></div>
<p></p>
<p>The idea here is to have the function return a list <b>L</b> of offsets such that:<br>
	<ul>
		<li>small_block_offset + lead_Pokemon_offset + 0x08 + L[0] is the first byte in block A</li>
		<li>small_block_offset + lead_Pokemon_offset + 0x08 + L[1] is the first byte in block B</li>
		<li>And so on</li>
	</ul>
</p>
<p>Now that we have bypassed the Pokemon data structure's encryption let's combine this with our general checksum bypass and try to edit a Pokemon's species id. We can play the game for a bit until we get our starter Pokemon and try to change it to a different Pokemon:</p>
<p></p>
<img class="center" src="/assets/images/glitch.png">
<p></p>
<p>Wait that doesn't look right... Of course, we forgot to update the Pokemon checksum!</p>
<p></p>
<p><b>Updating the Pokemon checksum:</b><br>
		Thankfully this is very straightforward, the Pokemon checksum consists simply of the 16 lower bits of the sum of bytes 0x08 to 0x87 (unencrypted). We can write a function to calculate and update the Pokemon checksum: 
</p>
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;padding:.2em .6em;"><pre style="margin: 0; line-height: 125%"><span style="color: #66d9ef">def</span> <span style="color: #a6e22e">updatePKMChecksum</span><span style="color: #f8f8f2">(data):</span>
	<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">=</span> <span style="color: #ae81ff">0</span>
	<span style="color: #f8f8f2">dataToUpdate</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">data[small_block_offset</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">lead_Pokemon_offset</span><span style="color: #f92672">+</span><span style="color: #ae81ff">0x08</span><span style="color: #f8f8f2">:small_block_offset</span><span style="color: #f92672">+</span><span style="color: #f8f8f2">lead_Pokemon_offset</span><span style="color: #f92672">+</span><span style="color: #ae81ff">0x87</span><span style="color: #f8f8f2">]</span>
	<span style="color: #66d9ef">for</span> <span style="color: #f8f8f2">i</span> <span style="color: #f92672">in</span> <span style="color: #f8f8f2">range(</span><span style="color: #ae81ff">0</span><span style="color: #f8f8f2">,len(dataToUpdate),</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">):</span>
		<span style="color: #f8f8f2">sum</span> <span style="color: #f92672">+=</span> <span style="color: #f8f8f2">([i</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">&lt;&lt;</span> <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">dataToUpdate[i]</span>

	<span style="color: #f8f8f2">data[small_block_offset</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">lead_Pokemon_offset</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">Pokemon_checksum_offset]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">int(bin(sum)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span>
	<span style="color: #f8f8f2">data[small_block_offset</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">lead_Pokemon_offset</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">Pokemon_checksum_offset</span><span style="color: #f92672">+</span><span style="color: #ae81ff">1</span><span style="color: #f8f8f2">]</span> <span style="color: #f92672">=</span> <span style="color: #f8f8f2">int(bin(sum</span> <span style="color: #f92672">&gt;&gt;</span> <span style="color: #ae81ff">8</span><span style="color: #f8f8f2">)[</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">:][</span><span style="color: #f92672">-</span><span style="color: #ae81ff">8</span><span style="color: #f8f8f2">:],</span><span style="color: #ae81ff">2</span><span style="color: #f8f8f2">)</span>
</pre></div>
<p></p>
<p>Finally we have all the tools we need to get a hacker worthy starter:</p>
<p></p>
<img class="center" src="/assets/images/starter.png">
<p></p>
<p><a name="Conclusion and future work"></a></p>
<p></p>
<hr style="border-top:1px solid #28323C;">
<p><h3>Conclusion and future work</h3></p>
<p>I ended up taking what I learned from this project and writing a terminal based save file editor for the 4th generation Pokemon games, you can find it on my github <a href="https://github.com/0xA2/Pokemon-Lite-Saveditor/"><b>[here's a link if you're interested]</b></a></p>
<p>Current features include:<br>
	<ul>
		<li>Editing player name</li>
		<li>Editing lead Pokemon's species</li>
		<li>Editing lead Pokemon's ability</li>
	</ul>
	Implementing things like the ability to edit a Pokemon's moves for example (which you'd think would be simple considering what we've managed so far) is on the TODO list. Speaking of which... when I tried editing moves:
</p>
<p></p>
<img class="center" src="/assets/images/glitch0.png">
<p></p>
<p>No idea why this happened (also, that was supposed to be a Charizard!).</p>
