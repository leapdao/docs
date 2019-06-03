Run the game
---
Execute following script in `client` folder:
```javascript
npm start
```
Open your browser at [http://localhost:1234](http://localhost:1234)
![](screenshots/step-1.jpg)


Activate Metamask:
![](screenshots/step-2.jpg)


Pick Rinkeby test network:
![](screenshots/step-3.jpg)

Now start the server by running in `nodemon start` in the `server` folder: 
![](screenshots/step-4.jpg)

Press "Continue":
![](screenshots/step-5.jpg)

Connect Metamask wallet to page:
![](screenshots/step-6.jpg)

Press "Play Game":
![](screenshots/step-7.jpg)

Press "Start Round":
![](screenshots/step-8.jpg)

You will see that client UI changed and that request hit the server
![](screenshots/step-9.jpg)

Pick one of the words:
![](screenshots/step-10.jpg)

Approve your bet(press "Sign" button on the right)
![](screenshots/step-11.jpg)

New request is sent to server
![](screenshots/step-12.jpg)

Round is processed on server, client got receipt back and show result of the round
![](screenshots/step-13.jpg)

You can press "New Round" button to start game anew
