# omaha-trainer.
Training app
<!DOCTYPE html>
<html>
<head>
    <title>Omaha Post-Flop Evaluator</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 20px; }
        select, button { margin: 5px; }
        #result, #board, #best-combo { margin-top: 20px; font-size: 18px; }
        .reason { font-style: italic; color: #555; }
    </style>
</head>
<body>
<h1>Omaha Post-Flop Combo Evaluator</h1>

<p>Select your 4 hole cards:</p>
<div>
    <div>Card 1: <select id="card1"></select> <select id="suit1">
        <option value="H">♥</option><option value="D">♦</option><option value="C">♣</option><option value="S">♠</option></select></div>
    <div>Card 2: <select id="card2"></select> <select id="suit2">
        <option value="H">♥</option><option value="D">♦</option><option value="C">♣</option><option value="S">♠</option></select></div>
    <div>Card 3: <select id="card3"></select> <select id="suit3">
        <option value="H">♥</option><option value="D">♦</option><option value="C">♣</option><option value="S">♠</option></select></div>
    <div>Card 4: <select id="card4"></select> <select id="suit4">
        <option value="H">♥</option><option value="D">♦</option><option value="C">♣</option><option value="S">♠</option></select></div>
</div>

<button onclick="simulateOmaha()">Simulate Post-Flop</button>

<div id="board"></div>
<div id="result"></div>
<div id="best-combo"></div>

<script>
const ranks = ["2","3","4","5","6","7","8","9","10","J","Q","K","A"];
for(let i=1;i<=4;i++){
    const select = document.getElementById("card"+i);
    ranks.forEach(rank => { let o = document.createElement("option"); o.value = rank; o.text = rank; select.add(o); });
}

// Draw random 5-card board
function drawBoard(){
    let deck=[];
    ranks.forEach(r=>["H","D","C","S"].forEach(s=>deck.push({rank:r,suit:s})));
    let board=[];
    for(let i=0;i<5;i++){
        let idx=Math.floor(Math.random()*deck.length);
        board.push(deck.splice(idx,1)[0]);
    }
    return board;
}

// Generate all 2-card combinations from hole cards
function getCombos(cards,suits){
    let combos=[];
    for(let i=0;i<4;i++){
        for(let j=i+1;j<4;j++){
            combos.push([{rank:cards[i],suit:suits[i]},{rank:cards[j],suit:suits[j]}]);
        }
    }
    return combos;
}

// Simple scoring for combination with board (pairs, suited, connected)
function scoreCombo(combo, board){
    let score=0;
    let allCards = combo.concat(board);
    let ranks = allCards.map(c=>c.rank);
    let suits = allCards.map(c=>c.suit);

    // Pairs
    let uniqueRanks=[...new Set(ranks)];
    let pairCount = ranks.length - uniqueRanks.length;
    score += pairCount*2;

    // Suited
    let suitSet = new Set(suits);
    if(suitSet.size<allCards.length) score+=1;

    // Connected (straight potential)
    let values = ranks.map(r=>{if(r==="J")return 11;if(r==="Q")return 12;if(r==="K")return 13;if(r==="A")return 14; else return parseInt(r)});
    values.sort((a,b)=>a-b);
    if(values[4]-values[0]<=10) score+=1;

    return score;
}

function simulateOmaha(){
    let cards=[],suits=[];
    for(let i=1;i<=4;i++){ cards.push(document.getElementById("card"+i).value); suits.push(document.getElementById("suit"+i).value); }

    const board = drawBoard();
    document.getElementById("board").innerText="Board: "+board.map(c=>c.rank+c.suit).join(", ");

    const combos = getCombos(cards,suits);
    let bestScore=-1, bestCombo=[];
    combos.forEach(c=>{
        let s=scoreCombo(c,board);
        if(s>bestScore){ bestScore=s; bestCombo=c; }
    });

    document.getElementById("best-combo").innerText="Best 2-card combo: "+bestCombo.map(c=>c.rank+c.suit).join(", ")+" (Score: "+bestScore+")";
}
</script>
</body>
</html>
