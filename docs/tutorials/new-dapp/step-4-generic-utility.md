Generic Methods
---
Let's create a couple of utility methods that will help us reduce code repetition and make our code cleaner.

In the root of our `server` folder create new `utils` folder and add single file with name `generic.js`
On several occasions we will need to remove "0x" from hexadecimal string and convert it to lower case. Add a method for this:  
```javascript
function sliceZero(str) {
  return str.replace("0x", "").toLowerCase();
}
```
Next one will look for all occurrences of the string and replace it:
```javascript
function replaceAll(str, find, replace) {
  return str.replace(new RegExp(find, "g"), sliceZero(replace));
}
```
**showLog** will make our output slightly more pretty and easy to follow:
```javascript
function showLog(data, title, lineCharacter = "-") {
  const LINE = "\n---------------\n".replace(/-/g, lineCharacter);
  console.log(LINE);
  if (title) {
    console.log(title.toUpperCase(), "\n");
  }
  console.log(data);
  console.log(LINE);
}
```
Now export those from the file, so we could use them later:
```javascript
module.exports = {
  showLog,
  replaceAll,
  sliceZero
};
```

Faucet
---
Inside the same `server/utils` folder create new file `faucet.js` and put following code that will send request to faucet
```javascript
const got = require("got");

async function requestFaucet(address, color) {
  console.log(`Requesting faucet for ${address} token ${color}`);
  const faucet =
    "https://dlsb7da2r7.execute-api.eu-west-1.amazonaws.com/staging/address";
  try {
    const response = await got(faucet, {
      method: "POST",
      json: true,
      body: {
        address,
        color
      }
    });
    const { body } = response;
    console.log(body);
    return body;
  } catch (error) {
    console.log(error.body.errorMessage);
    return false;
  }
}

module.exports = requestFaucet;
```
> *Please note, there is no financial advantage in generating multiple addresses to stash LEAP on staging. 
If you deplete faucet you will simply create a burden for other developers like you. Be polite!*


