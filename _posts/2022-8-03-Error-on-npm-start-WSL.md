---
layout: post
title: Error - spawn powershell.exe ENOENT in WSL
---

If you are running React app for the first time in WSL, some errors have the potential to kill your first joy of seeing the default react page getting loaded. But no, don't let the error get better of you! Let's decode one of the common errors and a way to straighten it out.


## Start a react-app
You can either git clone your react code or can create new react app. If git cloned, install all the dependencies and then start the app with ```yarn start``` or ```npm start```.

## Error 
Do you see the below error?

![Error](/images/error_powershell.png)


## Decode the error
In the above error, the line number says the exact line in the library used in our project. The error code is ENOENT (Error NO ENTry (or Error NO ENTity)) when the syscall to powershell.exe (on your host Windows) is made. 

Let's decode the encoded message on spawnargs ('-EncodedCommand'). Here I have used an [online Base64 Linux decoder](https://www.base64decode.org/)  to encode the decoded message. The encoded message reads ```Start "http://localhost:3001"```

![EncodedMessage](/images/base64.png)

If you run the encoded message on your windows powershell, it will open a browser with the URL. So, its now clear the WSL is trying to open a browser from the powershell.exe to serve the react application. 

## Let's solve it
One of the ways to solve this problem is to stop the browser from opening. We can open the browser by ourselves, don't we?
Let's create a .env file inside the project folder and add ```BROWSER=none```. React will parse your .env file and like an obidient child, it will understand your command to not open the browser from powershell. 

Run the app, and open the browser to see your app being served. :)

![ReactApp](/images/reactapp.png)

Good job with your first React App!

Happy coding!
