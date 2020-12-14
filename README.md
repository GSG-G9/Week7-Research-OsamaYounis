# Week7 Research Osama Younis
## HTTP vs HTTPS
Both are protocols that exchange information between web servers and the browser, but there are some additions in HTTPS that make it more secure compared to HTTP.

Some of these differences are that HTTP does not encrypt the data while HTTPS encrypt it before sending it,

![](https://i.imgur.com/upuSuc3.png)

also HTTP operates at [application layer](https://en.wikipedia.org/wiki/Application_layer), while HTTPS operates at [transport layer](https://en.wikipedia.org/wiki/Transport_layer), 
and HTTP does not request an SSL certificate but HTTPS You need to have a CA signed SSL certificate.

SO..
### What are TLS/SSL certificates?
Transport Layer Security (TLS) and Transport Layer Security are security protocols provide secure communication between devices.
An SSL Certificate is a small data file that takes advantage of this security protocol to authenticate, it creates credentials to certify the legitimacy of the website also used for a security data connection so that it activates the HTTPS protocol to secure the connection between the web server and the browser.



> #### note :
>  SSL was the predecessor of TLS, and the world began moving away from SSL once TLS was introduced in 1999, thanks to the improved security features of the latter. TLS is currently in its third iteration, and is called TLS 1.3.

now ..
### How do we create and use certificates in a node project?
#### 1- HTTPS
We will need to use the HTTPS module instead of the HTTP which is included in Nodejs by default.
```js
const https = require("https"),
fs = require("fs");

const options = {}

const app = express();

app.use((req, res) => {
  res.send("hello world");
});

app.listen(8000);

https.createServer(options, app).listen(8080);
```
Ignore options for a short time as we will put in SSL certificates that we will create later
#### 2-Generating Certificates
we need to create a self-signed certificate.
A self signed certificate is sufficient to create a secure HTTPS connection for development purposes Although, browsers will complain that the certificate is self-signed (and thus unreliable).

To create it we need [OpenSSL](https://www.openssl.org/) to our system

Once OpenSSL is installed, run this command:
```
openssl req -nodes -new -x509 -keyout server.key -out server.cert
```
than you will be asked to answer some questions

now, it well creat files for you where you ran the command
* `server.cert` is the self-signed certificate file
* `server.key` is the private key of the certificate

Now we will read these files and put them in options the App

```
const options = {  
  key: fs.readFileSync('server.key'),
  cert: fs.readFileSync('server.cert')
}
```
Chrome will tell us that the certificate is invalid (since it's self-signed), and will ask us to confirm before continuing (however, the HTTPS connection will still work)

## Stateless vs stateful authentication

stateful authentication is how to verify the user by having the server store much of the session information also Stateless authentication is a method for verifying the user but session information stored at the client not server


### Advantages and Disadvantages for both
#### Stateless:
- Advantages
  - Less effort on the server as it does not require storing all session data
- Disadvantages
    - The session cannot be canceled the session at any time because the session is stored with clients, so the server does not have the right to delete it whenever it need.
    - Session data cannot be changed until its expiration time

#### stateful:
- Advantages
    - Cancel the session at any time
    - Session data can be changed any time
- Disadvantages
    - more effort on the server

## Session-management in Express
When there is request and response from a same client to a server, the server cannot identify from which client it is getting requests. Because HTTP is a stateless protocol. When there is a need to maintain a state of conversation, session tracking is needed.

### Session-management example

````js
const express = require("express") 
const session = require('express-session') 
const app = express() 
   
app.use(session({ 
    secret: 'Secret_Key', 
    resave: true, 
    saveUninitialized: true
})) 
   
app.get("/", function(req, res){ 
    req.session.name = 'Osama-you'
    return res.send("Session is done") 
}) 
   
app.get("/session", function(req, res){ 
    const name = req.session.name 
    return res.send(name) 

}) 
    
app.listen(3000, function(){ 
    console.log(3000) 
}) 

````
Make sure you have install express and express-session
````
npm install express express-session 
````
## Attacks
### A man in the middle (MITM) 
When the Atacker puts himself in the communication between the user and the application, either for eavesdropping or impersonating one of the parties, this does not effect the exchange of information that is under implementation.

##### How to protect from it:
- Strong WEP / WPA encryption on access points to prevent unwanted users from joining your network once in a local location
- Strong Router Login Credentials
- Use HTTPS protocol, HTTPS can be used to securely communicate over HTTP using public-private key exchange.
### Cross Site Scripting (XSS)
It is a security vulnerability that is usually found on websites in which the attacker programmatically injects texts from the client-side, enabling him to gain access to unauthorized privileges.
##### How to protect from it:
- Filter the input value to avoid any script coming through it.

### Cross Site Request Forgery (CSRF)
It is an attack that tricks the victim into submitting a malicious request, to obtain the powers of this user

##### How to protect from it:
- This attack, the protection comes from the user, so that the user must not click on the links or anonymous e-mails, or to enter his data in untrusted sites