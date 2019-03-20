<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<script src="https://unpkg.com/animate-css-grid@1.4.0/dist/main.js"></script>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>PUZZLE</title>
<style>
* {
    box-sizing: border-box;
}

body {
    display: grid;
    place-items: center;
    height: 100vh;
    background: #111;
    color: #fefefe;
    font-family: 'Fontdiner Swanky', cursive;
    -webkit-font-smoothing: antialiased;
}

p {
    font-family: Helvetica, Arial, sans-serif;
}

.puzzle {
    background: linear-gradient(hsl(360, 98%, 36%), hsl(360, 92%, 42%)), url(https://www.toptal.com/designers/subtlepatterns/patterns/white_plaster.png);
    background-blend-mode: multiply;
    width: 90vw;
    max-width: 400px;
    box-shadow:
        0 .25em 1em 0 rgba(0,0,0,0.30),
        inset .25em .25em 1em 0 hsl(360, 88%, 72%),
        inset .15em .15em .2em 0 hsl(360, 88%, 72%),
        inset -.25em -.25em 1em 0 hsl(360, 88%, 22%),
        inset -.15em -.15em .2em 0 hsl(360, 88%, 2%);
    border-radius: 1.2em;
    padding: 1.4em;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-gap: .5em;
    position: relative;
    overflow: hidden;
}

.puzzle::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 40%;
    transform: rotate(-15deg) scale(2);
    background: linear-gradient(rgba(255,255,255,0.05), rgba(255,255,255,0.12));
}

.puzzle > * {
    z-index: 1;
}

.grid {
    border: 2px solid hsl(360, 98%, 12%);
    grid-column-end: span 3;
    display: grid;
    grid-gap: 2px;
    grid-template-areas:
        "A B C"
        "D E F"
        "G H I";
    background: hsl(360, 98%, 12%);
    box-shadow:
        inset 0 0 2em 0 hsl(360, 98%, 2%);
}

.tile,
.answer {
    height: 0;
    padding-bottom: 100%;
    grid-area: var(--area, auto);
    border: none;
    background: url(https://source.unsplash.com/900x900/?christmas,holiday,festive);
    background-size: 300%;
}

.tile {
    cursor: pointer;
}

.tile--empty {
    cursor: auto;
}

.tile:focus {
    outline: 2px solid hsl(360, 98%, 42%);
}

.tile[disabled] {
    cursor: not-allowed;
}

.answer {
    grid-column-end: span 1;
    width: 100%;
    justify-self: flex-end;
    background-size: 100%;
    box-shadow:
        inset 0 0 0 .2em hsl(360, 90%, 26%);
}

.tile--empty { background: transparent; }
.tile--1 { background-position: top left; }
.tile--2 { background-position: top center; }
.tile--3 { background-position: top right; }
.tile--4 { background-position: center left; }
.tile--5 { background-position: center; }
.tile--6 { background-position: center right; }
.tile--7 { background-position: bottom left; }
.tile--8 { background-position: bottom center; }

h2 {
    margin: 0;
    font-size: 1.8em;
}

.heading span {
    font-size: 1em;
    line-height: 2.4em;
}

.heading {
    align-self: center;
    grid-column-end: span 2;
    text-align: center;
    text-shadow: 0 -1px 0 hsl(360, 90%, 26%);
    transform: skew(-10deg) rotate(-6deg);
}

@keyframes popIn {
    from {
        opacity: 0;
        pointer-events: none;
        visibility: hidden;
        transform: scale(0.6);
        transition: opacity, scale, 600ms cubic-bezier(0.68, -0.55, 0.265, 1.55);
    }
}

p {
    padding: 0 1em;
    text-align: center;
}

a {
    color: white;
}
</style>
</head>
<body>

	<div class="puzzle">
		<div class="heading">
			<span>
				<sub>★</sub> <sup>☆</sup> <sub>★</sub> <sup>☆</sup> <sub>★</sub> <sup>☆</sup> <sub>★</sub>
			</span>
			<h2>puzzle</h2>
			<span>
				<sup>★</sup> <sub>☆</sub> <sup>★</sup> <sub>☆</sub> <sup>★</sup> <sub>☆</sub> <sup>★</sup>
			</span>
		</div>
		<div class="answer"></div>
		<div class="grid">
			<button class="tile tile--1" style="--area:A"></button>
			<button class="tile tile--2" style="--area:B"></button>
			<button class="tile tile--3" style="--area:C"></button>
			<button class="tile tile--4" style="--area:D"></button>
			<button class="tile tile--5" style="--area:E"></button>
			<button class="tile tile--6" style="--area:F"></button>
			<button class="tile tile--7" style="--area:G"></button>
			<button class="tile tile--8" style="--area:H"></button>
			<div class="tile tile--empty" style="--area:I"></div>
		</div>
	</div>

<script>
 // Initiate CSS Grid animation tool
const grid = document.querySelector(".grid");
const { forceGridAnimation } = animateCSSGrid.wrapGrid(grid);

// Get all the tiles and the empty tile
const tiles = Array.from(document.querySelectorAll(".tile"));
const emptyTile = document.querySelector(".tile--empty");

// Get congratulations heading
const heading = document.querySelector(".heading");

// A key / value store of what areas to "unlock"
const areaKeys = {
	A: ["B", "D"],
	B: ["A", "C", "E"],
	C: ["B", "F"],
	D: ["A", "E", "G"],
	E: ["B", "D", "F", "H"],
	F: ["C", "E", "I"],
	G: ["D", "H"],
	H: ["E", "G", "I"],
	I: ["F", "H"]
};

// Add click listener to all tiles
tiles.map(tile => {
	tile.addEventListener("click", event => {
		// Grab the grid area set on the clicked tile and empty tile
		const tileArea = tile.style.getPropertyValue("--area");
		const emptyTileArea = emptyTile.style.getPropertyValue("--area");

		// Swap the empty tile with the clicked tile
		emptyTile.style.setProperty("--area", tileArea);
		tile.style.setProperty("--area", emptyTileArea);

		// Animate the tiles
		forceGridAnimation();

		// Unlock and lock tiles
		unlockTiles(tileArea);
	});
});

// Unlock or lock tiles based on empty tile position
const unlockTiles = currentTileArea => {
	
	// Cycle through all the tiles and check which should be disabled and enabled
	tiles.map(tile => {
		const tileArea = tile.style.getPropertyValue("--area");

		// Check if that areaKey has the tiles area in it's values
		// .trim() is needed because the animation lib formats the styles attribute
		if (areaKeys[currentTileArea.trim()].includes(tileArea.trim())) {
			tile.disabled = false;
		} else {
			tile.disabled = true;
		}
	});

	// Check if the tiles are in the right order
	isComplete(tiles);
};


const isComplete = tiles => {
	
	// Get all the current tile area values
	const currentTilesString = tiles
		.map(tile => tile.style.getPropertyValue("--area").trim())
		.toString();

	// Compare the current tiles with the areaKeys keys
	if (currentTilesString == Object.keys(areaKeys).toString()) {
		heading.children[1].innerHTML = "You win!";
		heading.style = `
			animation: popIn .3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
		`;
	}
};


// Inversion calculator
const inversionCount = array => {
	
	// Using the reduce function to run through all items in the array
	// Each item in the array is checked against everything before it
	// This will return a new array with each intance of an item appearing before it's original predecessor
	return array.reduce((accumulator, current, index, array) => {
		return array
			.slice(index)
			.filter(item => {
				return item < current;
			})
			.map(item => {
				return [current, item];
			})
			.concat(accumulator);
	}, []).length;
};


// Randomise tiles
const shuffledKeys = keys => Object.keys(keys).sort(() => .5 - Math.random());

setTimeout(() => {

	// Begin with our in order area keys
	let startingAreas = Object.keys(areaKeys);
		
	// Use the inversion function to check if the keys will be solveable or not shuffled
	// Shuffle the keys until they are solvable
	while (inversionCount(startingAreas) % 2 == 1 || inversionCount(startingAreas) == 0) {
		startingAreas = shuffledKeys(areaKeys);
	}	

	// Apply shuffled areas
	tiles.map((tile, index) => {
		tile.style.setProperty("--area", startingAreas[index]);
	});

	// Initial shuffle animation
	forceGridAnimation();

	// Unlock and lock tiles
	unlockTiles(emptyTile.style.getPropertyValue("--area"));
}, 2000);

</script>
</body>
</html>
