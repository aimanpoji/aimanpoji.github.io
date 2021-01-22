![lazy](?raw=true)
 This is the write-up for Pentest Technical Challenge for my job application. This web application is a demonstration of common server-side application flaws. Each of the vulnerabilities has its own difficulty rating. This web is available open source. [Link](https://github.com/RamadhanAmizudin/lazyweb)
***
## Broken Authentication

Upon login, each user wil be set their own user_id. Which in my case my user_id is set to 5. 
If we change the user_id value to other value, it will also change user. 
Proof of concept as below.

![broken1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken1.png?raw=true)

![broken2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broke2.png?raw=true)


Changed user_id value to 3 and we get user ayed

![broken3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken3.png?raw=true)

![broken4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken4.png?raw=true)
