***Tryhackme Hammer CTF Walkthrough***

Description : Use your exploitation skills to bypass ***authentication mechanisms*** on a website and get RCE.

![Hammer ctf walk through](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*r3j4i6SC65453qMJjcUB1Q.png)


Hi everyone ! , In this ctf we have to find two flags for completing this room

**1)** What is the flag value after logging in to the dashboard?

**2)** What is the content of the file **/home/ubuntu/flag.txt**?

After starting machine we got an ip address , then first we have to find what are the open ports available , for this task i used Rustscan for fast port scanning you can use nmap but it is slow as compared to rustscan

![port scanning](https://miro.medium.com/v2/resize:fit:1190/format:webp/1*h1dZkMi_cYFHYsBi_cVcTQ.png)

As we can see two ports are open , one is **22** It allows users to securely execute commands, transfer files, and manage network infrastructure from a remote location and second port is custom port **1337** so when i navigate to <ip address>:1337 port number it demonstrate me a login page

![login](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Y2M-ZCPIyEIqqamDP1eC2w.png)

first thing that came to my mind is brute forcing but in this case i don’t have any idea about email address/usernames and password. 
so then I try to find all kind of relevant information that plays a role in logging in to dashboard

![curl command sending post request](https://miro.medium.com/v2/resize:fit:1176/format:webp/1*HlQOIK9mNOV0qv1i6Vndgw.png)

With curl Tool , I send a **POST request** to login page by email address admin@40thm.com along with password “pass” for inspecting the behaviour of app.

![curl post request results](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*d8a29XJnLzhyZo9PuwNPuA.png)

As we can see in the code , there is **Dev note : directory naming convention must be hmr_ .** It means every directory start with **hmr _directory** like **_**hmr_admin , hmr_api etc

![directory fuzzing](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Pd6OOQdYjz0q8AuOvuyf2A.png)

So with ffuf i found all the directories starting with **hmr .** then i navigate to all the directories one by one i didn’t found any helpful information within **images , css , js** directory . But when i deep down In **logs** directory I found an another directory **error.logs**

![found an email address](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*36-hlF73-So6tLag9AvfMw.png)

when I opened error.logs as we can see I found an email address **tester@hammaer.thm** which will helps in further steps . By knowing email address we can test the password reset functionality

![Reset password](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*6Kz92DufhGr0k7XXDF6fJQ.png)

I entered an email address and hit submit button then it demands a four digit recovery code for reset the password

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*g8hDvP5464te0kGUWZrHOw.png)

But the important thing it sets a timer of 180 seconds and rate limit . so after entering 7 failed login attempts it will display a message “rate limit exceeded try again later “ . and after 180 seconds it expire our session

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*CZ6Rj7vcPNOPAfIwLZqZ2w.png)

At this point I stuck a lot then I google it I found this [**script**](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbkhRUHhNTk5BWjRSU1Z4cnhpQzQ1dHNNQnJuUXxBQ3Jtc0trWEY0TkJydGZ3M3piZl9za3ZWR1ljdHMza1VyeE9fdFM3ZzdTbGdJTE9UQ0FtUWt1bzF0djVNZGFZMTBQU3ozN0xiQ3lhelA1LVVGNF9KbXlfdFhjZkV5dXRzSFRWLWhaRzlqYW9idndDR0Y4TmREdw&q=https%3A%2F%2Fgithub.com%2FMatSec21%2FHammerTHM%2Fblob%2Fabc121ccb61f832ebe33becf4063189d9482dd75%2Fbruteforcecodes.py&v=Y8-ahp7mnLI) **.** what does this script do it performs a **brute-force attack** on a **password reset recovery code**
It attempts to discover a **4-digit recovery code** by sending multiple parallel requests while spoofing the client IP address using the `X-Forwarded-For` header (to try bypassing rate limits or IP-based blocks).

In this script url = “http://10.10.123.224:1337/reset_password.php" you have to change the ip address in a script according to your machine ip address

![python script](https://miro.medium.com/v2/resize:fit:760/format:webp/1*jJZR-C4AyweNJpQlP7K2vg.png)

After finding the recovery code it also set the password to **“Passowrd123**” then now we can login easily.

![flag 1](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*XPDFm9CVXz9_HkMfGLH35Q.png)

As we can see after logging into dashboard we received a flag

![burpsuite request](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*lLDfyAA0g_tBM-foIzzq5g.png)
 
==========================================

After the capturing the request in a burpsuite navigate to burp repeater send the request you got a jwt token . also when i try to execute “cat” command it gives an error “command not allowed “so in this scenario we have to escalate our privileges to admin . I decode the JWT token via JWT debugger you can also use jwt.io.

![deocoded jwt token](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*7R_FQkOV6uvb1mBpRVjleg.png)

we also required a **hs256** key for signing JWT token after implement changes in a header and payload section. In a dashboard when I entered a “**ls** “ it showed me a key **188ade1.key .** when I navigate to /188ade1.key it downloaded automatically

![key](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*xQzOPjTj9GKTzitnjekiXg.png)![key](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*hWqCLUQSkrSqci11yemZAg.png)

Implement changes in a payload section , change role “**user**” to “**admin**”.and in the header section change “**kid :var/www.mykey.key** “to “ **var/www/html/188ade1.key “**

Now sign the token you can use **jwt.io** online website ,but I used a python script for generating JWT token

![python script](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*jvf1r9oaRFM5TnBWe-mnUQ.png)![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*D8kr0F2wamIfvfg4QY3ZUQ.png)

Now the copy this jwt token and paste it on burpsuite request

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*r3F0m04d2GeaDsQC0_dyXQ.png)

Now as you can see cat command executing perfectly this means now we have admin privileges. btw this is a key that we used for signing JWT token earlier.

At last write this command **cat /home/ubuntu/flag.txt** you will get a flag

![flag 2](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*6G6UAyJuyHZN_3XWQia2pQ.png)

⭐ If you find these writeup helpful, consider giving this repo a **star** to support my work!  


You can also follow me on [**LinkedIn**](https://www.linkedin.com/in/muhammad-ahsanijaz/) or [**GitHub**](https://github.com/AhsanIjaz17) for more.

