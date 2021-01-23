![lazy](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/Lazy.PNG?raw=true)

## This is the write-up for a Penetration Testing Technical Challenge for my job application. The web application is a demonstration of common server-side application flaws. Each of the vulnerabilities has its own difficulty rating. The web is now available open source. Link: [https://github.com/RamadhanAmizudin/lazyweb.](https://github.com/RamadhanAmizudin/lazyweb)

***

## 1. Broken Authentication

### Upon login, each user wil be set their own user_id. Which in my case my user_id is set to 5. If we change the user_id value to other value, it will also change user. Proof of concept as below.

![broken1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken1.png?raw=true)

![broken2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broke2.png?raw=true)


### Changed user_id value to 3 and we get user ayed

![broken3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken3.png?raw=true)

![broken4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/broken4.png?raw=true)

***

## 2. Sensitive Directories Exposed

### In /backup directory there is a file named db.sql which contain sensitive informations, database name and tables names. Also phpinfo.php is exposed which have information about php used for this web. Robots.txt contain s3cretadm1n page for admins. POC as below:

### Nikto scan results

![sens1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sens1.png?raw=true)

![sens2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sens2.png?raw=true)

### Sensitive informations in db.sql file

![sens3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sens3.png?raw=true)

### phpinfo page
![sens4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sens4.png?raw=true)

***

## 3. SQL Injection

### The email parameter in /user/login.php is vulnearable to time based blind injection. POC as below:

### Using sqlmap to check the vulnerable parameter.

![sql1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sql1.png?raw=true)

![sql2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sql1.png?raw=true)

![sql3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sql3.png?raw=true)

### Since we already know the database tables for `user` database, let just dump table `tbl_user`.

![sql4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sql4.png?raw=true)

### List of some entries dumped in table `tbl_user`

![sql5](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/sql5.png?raw=true)

***

## 4. Local File Inclusion

### n /page.php, page parameter is vulnerable to LFI. The method used is lfi using wrapper and the wrapper used is php://filter. POC as below: 

### This wrapper only read php file and convert it to base64.

![lfi1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/lfi1.png?raw=true)

### Decode the string and we get the code of page.php

![lfi2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/lfi2.png?raw=true)

***

## 5. Unrestricted file upload

### Since we have lfi we can get the code for profile.php which has upload feature for user to change their profile picture. POC as below:

![up1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/up1.png?raw=true)

### From this code, any file uploaded will be renamed to the user_id value with .png extension into directory /user/avatar. I have uploaded a php file and it stored to the server as php file with .png extension.

![up2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/up2.png?raw=true)

![up3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/up3.png?raw=true)

***

## 6. RCE from LFI

### From phpinfo.php, phar wrapper is enabled. Phar pack files of a module into a package like .jar in java. We pack php shell file into phar file and upload it to get a command execution. POC as below:

### Phar enabled

![phar1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar1.png?raw=true)

### Make phar file using php code below

![phar2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar2.png?raw=true)

### Run the script and phar file will be created.

![phar3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar3.png?raw=true)

![phar4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar4.png?raw=true)

### Upload the phar file to the server and execute any command.

![phar5](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar5.png?raw=true)

![phar6](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/phar6.png?raw=true)

***

## 7. Admin Authorization to normal user

### In robot.txt we found a s3cretadm1n page. This page only allows admin to access. If we change normal user to have admin access, we as a normal user, we can access the page. POC as below:

### Code for s3cretadm1n/index.php

![admin1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin1.png?raw=true)

### From this code if we are not admin it will redirect back to normal page. If admin, this page allows users to execute command.

### There is a function that handles sessions called save_session.php. Code for save_session.php

![admin2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin2.png?raw=true)

### Send request value debug = 1 to print our session data and add admin value =1.

![admin3](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin3.png?raw=true)

![admin4](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin4.png?raw=true)

### Now we have admin = 1 which mean we are authorized as admin. Go to s3cretadm1n page and execute any command.

![admin5](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin5.png?raw=true)

![admin6](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/admin6.png?raw=true)

***

## 8. OS Command Injection

### Url form in add-site.php does not filter input from user. Fill in the url form with our payload then, click ping and our command will be injected. POC Below: 

![os1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/os1.png?raw=true)

***

## 9. Stored xss

### The url form in add-site.php is vulnerable to xss. POC as below:

### The payload used was `\<a onmouseover="alert(document.cookie)"\>xxs link\</a\>`

![xss1](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/xss1.png?raw=true)

### Click submit and hover the mouse to the list sites. A pop out will show the cookie information of the user.

![xss2](https://github.com/aimanpoji/aimanpoji.github.io/blob/main/images/lazyweb/xss2.png?raw=true)


***

#### Referrences: 
##### 1. [https://owasp.org/www-community/xss-filter-evasion-cheatsheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)
##### 2. [https://www.jianshu.com/p/3b09ab7487e7](https://www.jianshu.com/p/3b09ab7487e7)
##### 3. [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion#lfi--rfi-using-wrappers](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion#lfi--rfi-using-wrappers)


