Init Client Interface
---
Go to `client` folder and create `package.json`
```json
{
  "name": "client",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "dev": "parcel src/index.html"
  },
  "browserslist": [
    "since 2017-06"
  ],
  "devDependencies": {
    "parcel-bundler": "^1.12.3"
  },
  "dependencies": {
    "ethers": "^4.0.27",
    "leap-core": "0.30.1",
    "web3": "1.0.0-beta.36"
  }
}
```
After it run `npm install` and wait while it finish fetching all packages.

> *If you on Windows and encounter and errors during the process, try to install
[windows-build-tools module](https://www.npmjs.com/package/windows-build-tools)* then run `npm install` again

HTML Layout
---
Make new folder and call it `src`, then add `index.html` file to it:
```html
<html lang="en">

<head>
	<meta charset="UTF-8">
	<meta name="viewport"
	      content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>Word Game</title>
	<script src="./index.js"></script>
	<link href="https://fonts.googleapis.com/css?family=Lato:400,900&display=swap" rel="stylesheet">
	<link rel="stylesheet" type="text/css" href="./styles.css">
</head>

<body>
<div id="app" class="main">
	<div class="step step--active" data-step="intro">
		<h1 class="header">Welcome to Plasma Word Game</h1>
		<p class="copy">Rules are simple - guess the word Game Master selected and win some tokens!</p>
		<button class="nav-button">Continue</button>
	</div>

	<div class="step" data-step="connect-wallet">
		<h1 class="header">Allow Wallet Connection</h1>
		<p class="copy">In order to sign and send transactions you need to connect your wallet</p>
		<p class="progress">Waiting for MetaMask connection...</p>
		<button class="nav-button">Activate Wallet</button>
	</div>

	<div class="step" data-step="game-rules">
		<h1 class="header">Make your move</h1>
		<p class="copy">You will be presented with choise of 3 words and you need to pick correct</p>
		<button class="nav-button">Play Game</button>
	</div>

	<div class="step" data-step="game-round">
		<h1 class="header">Guess the Word</h1>
		<div class="game-field">
		</div>
		<p class="progress">Fetching data from server...</p>
		<button class="nav-button">Start Round</button>
	</div>

	<div class="step step--lose" data-step="lose">
		<h1 class="header">You Lost!</h1>
		<p class="copy">Better luck next time!</p>
		<button class="nav-button">New Round</button>
	</div>

	<div class="step step--win" data-step="win">
		<h1 class="header">You Won!</h1>
		<p class="copy">Well done. Try again!</p>
		<button class="nav-button">New Round</button>
	</div>
</div>
</body>
</html>
```

Styles
---
Next add `styles.css`:
```css
body {
  display: flex;
  align-items: center;
  margin: 0;
  font-family: "Lato", sans-serif;
}
.main {
  display: flex;
  background-color: #eee;
  min-height: 100vh;
  flex: 1;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.step{
	display: none;
	flex-direction: column;
	align-items: center;
	background-color: rgb(245,245,245);
	box-shadow: 0 10px 20px 2px rgba(0,0,0,0.1);
	padding: 40px;
	border-radius: 6px;
	max-width: 600px;
	text-align: center;
	box-sizing: border-box;
}
.step--active{
	display: flex;
}

.step--lose{
	color: white;
	background-color: #da6565;
}

.step--lose button{
	background-color: #ad4747;
}

.step--win{
	color: white;
	background-color: #3da052;
}

.step--win button{
	background-color: #247b36;
}

h1.header {
	font-weight: 900;
  font-size: 32px;
  margin: 0 0 12px;
}

p{
	margin: 0;
}

p.copy {
  font-size: 16px;
  margin: 0 0 24px;
}

p.progress{
	font-style: italic;
	font-size: 14px;
	color: #555;
	display: none;
}

button{
	border: none;
	font-size: 16px;
	text-transform: uppercase;
	font-weight: 700;
	letter-spacing: 1px;
	padding: 16px 20px;
	flex: 0 0 auto;
	border-radius: 4px;
	cursor: pointer;
}

.nav-button{
	color:white;
	background-color: green;
	box-shadow: 0 3px 5px 1px rgba(0,0,0,0.1);
}

.word-toast{
	margin-right: 10px;
	margin-bottom: 10px;
	background-color: #ccc;
}

.word-toast:last-of-type{
	margin: 0;
}

.word-toast:hover{
	background-color: #39a2c1;
	color: white;
}
```

Utility Methods
---
Following with `utility.js`
```javascript
export const selectActiveStep = () => {
  return document.querySelector(".step--active");
};

export const selectStep = step => {
  return document.querySelector(`.step[data-step="${step}"]`);
};

export const showStep = activeStep => {
  selectActiveStep().classList.remove("step--active");
  selectStep(activeStep).classList.add("step--active");
};

const renderWord = word => `<button class="word-toast">${word}</button>`;
const renderWordSet = words =>
  words.reduce((acc, word) => acc + renderWord(word), "");

export const hideGameField = () => {
  document.querySelector(".game-field").style.display = "none";
};

export const showGameField = () => {
  document.querySelector(".game-field").style.display = "block";
};

//export const gameField = document.querySelector(".game-field");
export const clearGameField = () => {
  document.querySelector(".game-field").innerHTML = "";
};

export const setGameField = words =>
  (document.querySelector(".game-field").innerHTML = renderWordSet(words));

export const turn = (state, node) => {
  if (state === "on") {
    node.style.display = "block";
  } else {
    node.style.display = "none";
  }
};

export const activateWallet = async () => {
  // Let's assume that Metamask is installed and "ethereum" field
  // is exposed on "window" object
  const { ethereum } = window;
  return await ethereum.enable();
};

export const fetchPost = async (endpoint, payload) => {
  const headers = {
    Accept: "application/json",
    "Content-Type": "application/json"
  };
  const transport = {
    method: "POST",
    mode: "cors",
    cache: "default",
    headers,
    body: JSON.stringify(payload)
  };
  return await fetch(endpoint, transport).then(async response => {
    return await response.json().then(json => json);
  });
};
```

Now we can start implementing `index.js` file
