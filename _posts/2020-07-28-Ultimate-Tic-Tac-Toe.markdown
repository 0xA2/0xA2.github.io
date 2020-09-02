---
layout: default
title: "Ultimate Tic-Tac-Toe"
description: "[project] Javascrip implementation of the fun version of Tic-Tac-Toe"
categories: "Project"
---

<style type="text/css">

:root {
	--big-square-size: 220px;
	--small-square-size: 71px;
	--font-size: 73px;
}

.macroBoard {
	display:grid;
	justify-content: center;	
	align-content: center;
	grid-template-columns: repeat(3,auto);
}


.microBoard {
width:var(--big-square-size);
height:var(--big-square-size);
background-color: black;
border: 5px solid white;
display: grid;
justify-content: center;
justify-items: center;
align-items: center;
grid-template-columns: repeat(3,auto);
}

.microBoard.x::before {
	content: "";
	display: block;
	width: calc(var(--big-square-size));
	height: calc((var(--big-square-size)/3));
	position: absolute;
	background-color: hotpink;
	border-radius: 50px;
	transform: rotate(45deg);
}

.microBoard.x::after{
	content: "";
	display: block;
	width: calc(var(--big-square-size));
	height: calc((var(--big-square-size)/3));
	position: absolute;
	background-color: hotpink;
	border-radius: 50px;
	transform: rotate(-45deg);
}


.microBoard.o::before{
	content: "";
	display: block;
	width: calc(var(--big-square-size) * .9);
	height: calc(var(--big-square-size) * .9);
	position: absolute;
	background-color: hotpink;
	border-radius: 50%;
}

.microBoard.o::after{
	content: "";
	display: block;
	width: calc(var(--big-square-size) * .5);
	height: calc(var(--big-square-size) * .5);
	position: absolute;
	background-color: black;
	border-radius: 50%;
}

.microBoard.draw::after{
	content: "";
	display: block;
	width: calc(var(--big-square-size) * .8);
	height: calc(var(--big-square-size) * .8);
	position: absolute;
	background-color: hotpink;
}


.cell {
	width:var(--small-square-size);
	height:var(--small-square-size);
	background-color: black;
	border: 1px solid white;
	display: flex;
	justify-content: center;
	align-items: center;
}

.cell.x::before,
.cell.x::after,
.cell.o::before{
	background-color: hotpink;
}

.cell.o::after{
	background-color: black;
}

.microBoard.xHover .cell:not(.x):not(.o):hover::before,
.microBoard.xHover .cell:not(.x):not(.o):hover::after{
	background-color: grey;
}

.microBoard.oHover .cell:not(.x):not(.o):hover::before{
	background-color: grey;
}

.microBoard.oHover .cell:not(.x):not(.o):hover::after{
	background-color: black;
}


.cell.x::before,
.cell.x::after,
.microBoard.xHover .cell:not(.x):not(.o):hover::before,
.microBoard.xHover .cell:not(.x):not(.o):hover::after{
	content: "";
	display: block;
	width: calc(var(--small-square-size));
	height: calc((var(--small-square-size)/3));
	position: absolute;
	border-radius: 50px;
}

.cell.x::before,
.microBoard.xHover .cell:not(.x):not(.o):hover::before{
	transform: rotate(45deg);
}

.cell.x::after,
.microBoard.xHover .cell:not(.x):not(.o):hover::after{
	transform: rotate(-45deg);
}


.cell.o::before,
.microBoard.oHover .cell:not(.x):not(.o):hover::before{
	content: "";
	display: block;
	width: calc(var(--small-square-size) * .9);
	height: calc(var(--small-square-size) * .9);
	position: absolute;
	border-radius: 50%;
}

.cell.o::after,
.microBoard.oHover .cell:not(.x):not(.o):hover::after{
	content: "";
	display: block;
	width: calc(var(--small-square-size) * .5);
	height: calc(var(--small-square-size) * .5);
	position: absolute;
	border-radius: 50%;
}

.resetButton {
	display: block;
	margin: 0 auto;
	cursor: pointer
	margin-bottom: 10px;
}

.restart {
	display: none;
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	color: white;
	font-size: var(--font-size);
	background-color: black;
	opacity: .90;
	justify-content: center;
	align-items: center;
	flex-direction: column;
}

.restart.show {
	display: flex;
}
</style>

<title>Ultimate Tic-Tac-Toe - LostMyPlaintext</title>
<div class="well article">
	<h1 style="text-align: center;">Ultimate Tic-Tac-Toe</h1>
</div>

<!-- Main Board  -->
<div class = "macroBoard" id = "macroBoard">
	<div class="microBoard" microBoard>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
		<div class="cell" id="0" cell0></div>
	</div>			
	<div class="microBoard" microBoard>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
		<div class="cell" id="1" cell1></div>
	</div>						
	<div class="microBoard" microBoard>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
		<div class="cell" id="2" cell2></div>
	</div>			
	<div class="microBoard" microBoard>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
		<div class="cell" id="3" cell3></div>
	</div>			
	<div class="microBoard" microBoard>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
		<div class="cell" id="4" cell4></div>
	</div>			
	<div class="microBoard" microBoard>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
		<div class="cell" id="5" cell5></div>
	</div>							
	<div class="microBoard" microBoard>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
		<div class="cell" id="6" cell6></div>
	</div>			
	<div class="microBoard" microBoard>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
		<div class="cell" id="7" cell7></div>
	</div>						
	<div class="microBoard" microBoard>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
		<div class="cell" id="8" cell8></div>
	</div>			
</div>
<p></p>

<!-- Reset Button -->
<div>
	<button class="resetButton" id="resetButton">Reset Board</button>
</div>
<p></p>

<!-- Rules -->  
<div class="well article">
	<h2>Rules</h2>
	<h4>Each small 3 × 3 tic-tac-toe board is referred to as a local board, and the larger 3 × 3 board is referred to as the global board.</h4>
	<p></p>
	<h4>The game starts with X playing wherever they want in any of the 81 empty spots. This move "sends" their opponent to its relative location. For example, if X played in the bottom left square of their local board, then O needs to play next in the local board at the bottom left of the global board. O can then play in any one of the nine available spots in that local board, each move sending X to a different local board.</h4>
	<p></p>
	<h4>If a move is played so that it is to win a local board by the rules of normal tic-tac-toe, then the entire local board is marked as a victory for the player in the global board.</h4>
	<p></p>
	<h4>Once a local board is won by a player or it is filled completely, no more moves may be played in that board. If a player is sent to such a board, then that player may play in any other board.</h4>
	<p></p>
	<h4>Game play ends when either a player wins the global board or there are no legal moves remaining, in which case the game is a draw.</h4>
	<p></p>
</div>

<div class="restart" id="restart">
	<div message></div>
	<button id="exitButton">Play Again</button>
</div>

<script type="text/javascript">
	
const x = 'x'
const o = 'o'

// True if it's X's turn, false if it's O's turn
let xturn

const macroBoard = document.getElementById('macroBoard')

const microBoardElems = document.querySelectorAll('[microBoard]')

const cellElements0 = document.querySelectorAll('[cell0]')

const cellElements1 = document.querySelectorAll('[cell1]')

const cellElements2 = document.querySelectorAll('[cell2]')

const cellElements3 = document.querySelectorAll('[cell3]')

const cellElements4 = document.querySelectorAll('[cell4]')

const cellElements5 = document.querySelectorAll('[cell5]')

const cellElements6 = document.querySelectorAll('[cell6]')

const cellElements7 = document.querySelectorAll('[cell7]')

const cellElements8 = document.querySelectorAll('[cell8]')

const reset = document.getElementById('resetButton')

const winning = [
	[0,1,2],
	[3,4,5],
	[6,7,8],
	[0,3,6],
	[1,4,7],
	[2,5,8],
	[0,4,8],
	[2,4,6]
]

startGame()

reset.addEventListener('click', startGame)

function startGame(){
	xturn = true

	microBoardElems.forEach(microBoard => {
		microBoard.classList.remove(x)
		microBoard.classList.remove(o)
		microBoard.classList.remove('draw')
	})

	cellElements0.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements1.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements2.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements3.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements4.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements5.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements6.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements7.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	cellElements8.forEach(cell => {
		cell.classList.remove(x)
		cell.classList.remove(o)
		cell.addEventListener('click', handleClick, { once: true })
	})

	setHover()
}

function handleClick(e){

	const curCell = e.target
	const curMark = getCurMark()
	let flag = false
	if ( !microBoardElems[curCell.id].classList.contains(x) && !microBoardElems[curCell.id].classList.contains(o) && !microBoardElems[curCell.id].classList.contains('draw')){
		placeMark(curCell, curMark)
		flag = true
	}

	if (checkMicroWin(curCell,curMark)){
		switch(curCell.id){
			case '0':
				cellElements0.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '1':
				cellElements1.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '2':
				cellElements2.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '3':
				cellElements3.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '4':
				cellElements4.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '5':
				cellElements5.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '6':
				cellElements6.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '7':
				cellElements7.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
			break;
			case '8':
				cellElements8.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
		}
		microBoardElems[curCell.id].classList.add(curMark)
	}


	if (checkMicroDraw(curCell)){
		switch(curCell.id){
			case '0':
				cellElements0.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '1':
				cellElements1.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '2':
				cellElements2.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '3':
				cellElements3.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '4':
				cellElements4.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '5':
				cellElements5.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '6':
				cellElements6.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '7':
				cellElements7.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
			case '8':
				cellElements8.forEach(cell => {
					cell.classList.remove(x);
					cell.classList.remove(o);
				})
				break;
		}
		microBoardElems[curCell.id].classList.add('draw')
	}


	if (checkMacroWin(curMark)){
		if (curMark == x){
			alert('X wins')
		}
		else{
			alert('O wins')
		}
		startGame()
	}

	if (checkMacroDraw()){
		alert("Draw")
		startGame()
	}

	if (flag){
		switchTurns()
	}
	setHover()
}

function getCurMark(){
	if (xturn) {
		return x
	}
	else{
		return o
	}
}

function placeMark(curCell, curMark){
	 curCell.classList.add(curMark)
}

function switchTurns(){
	xturn = !xturn
}

function setHover(){
	microBoardElems.forEach(board => {
		board.classList.remove('xHover')
		board.classList.remove('oHover')
	})

	if (xturn) {
		microBoardElems.forEach(board => {
			if ( !board.classList.contains(x) && !board.classList.contains(o) && !board.classList.contains('draw')){
				board.classList.add('xHover')
			}
		})
	}
	else{
		microBoardElems.forEach(board => {
			if ( !board.classList.contains(x) && !board.classList.contains(o) ){
				board.classList.add('oHover')
			}
		})
	}
}

function checkMicroWin(curCell,curMark){
	return winning.some(combinations => {
		return combinations.every(i => {
			switch(curCell.id){
				case '0':
					return cellElements0[i].classList.contains(curMark);
					break;
				case '1':
					return cellElements1[i].classList.contains(curMark);
					break;
				case '2':
					return cellElements2[i].classList.contains(curMark);
					break;
				case '3':
					return cellElements3[i].classList.contains(curMark);
					break;
				case '4':
					return cellElements4[i].classList.contains(curMark);
					break;
				case '5':
					return cellElements5[i].classList.contains(curMark);
					break;
				case '6':
					return cellElements6[i].classList.contains(curMark);
					break;
				case '7':
					return cellElements7[i].classList.contains(curMark);
					break;
				case '8':
					return cellElements8[i].classList.contains(curMark);
					break;
			}
		})
	})
}

function checkMicroDraw(curCell){
	switch(curCell.id){
		case '0':
			return [...cellElements0].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '1':
			return [...cellElements1].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '2':
			return [...cellElements2].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '3':
			return [...cellElements3].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '4':
			return [...cellElements4].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '5':
			return [...cellElements5].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '6':
			return [...cellElements6].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '7':
			return [...cellElements7].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;
		case '8':
			return [...cellElements8].every(cell => {
				return cell.classList.contains(x) || cell.classList.contains(o)
			})
			break;

	}

}


function checkMacroWin(curMark){
	return winning.some(combinations => {
		return combinations.every(i => {
			return microBoardElems[i].classList.contains(curMark);
		})
	})
}

function checkMacroDraw(){
	return [...microBoardElems].every(microBoard => {
		return microBoard.classList.contains(x) || microBoard.classList.contains(o) || microBoard.classList.contains('draw')
	})
}

</script>
