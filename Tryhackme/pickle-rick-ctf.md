Pickle Rick -Tryhackme CTF walk through
=======================================

[![Muhammad Ahsan Ijaz](https://miro.medium.com/v2/resize:fill:64:64/1*ZPj4HqjlCzh2TsxaXltgLQ.jpeg)](https://medium.com/@ahsanijaz1?source=post_page---byline--0e7b4ab7ebf3---------------------------------------)

[Muhammad Ahsan Ijaz](https://medium.com/@ahsanijaz1?source=post_page---byline--0e7b4ab7ebf3---------------------------------------)

5 min read

·

Aug 17, 2025

[nameless link](https://medium.com/m/signin?actionUrl=https%3A%2F%2Fmedium.com%2F_%2Fvote%2Fsystem-weakness%2F0e7b4ab7ebf3&operation=register&redirect=https%3A%2F%2Fsystemweakness.com%2Fpickle-rick-tryhackme-ctf-walk-through-0e7b4ab7ebf3&user=Muhammad+Ahsan+Ijaz&userId=e8b18099f9da&source=---header_actions--0e7b4ab7ebf3---------------------clap_footer------------------)

--

[nameless link](https://medium.com/m/signin?actionUrl=https%3A%2F%2Fmedium.com%2F_%2Fbookmark%2Fp%2F0e7b4ab7ebf3&operation=register&redirect=https%3A%2F%2Fsystemweakness.com%2Fpickle-rick-tryhackme-ctf-walk-through-0e7b4ab7ebf3&source=---header_actions--0e7b4ab7ebf3---------------------bookmark_footer------------------)

Listen

Share

This Rick and Morty-themed challenge requires you to exploit a web server and find three ingredients to help Rick make his potion and transform himself back into a human from a pickle.

![Pickle Rick](https://miro.medium.com/v2/resize:fit:1004/format:webp/1*ip2ih9ZJcj4RrysV8Uq5pw.png)

Hi everyone , In this blog i included all the enumeration & exploitation steps that i took for finding all theses following flags

*   **Q1:**What is the first ingredient that Rick needs?
*   **Q2:**What is the second ingredient in Rick’s potion?
*   **Q3:**What is the last and final ingredient?

So lets dive in first with Reconnaissance(initial information gathering)
we given an ip address by “starting machine”,now first we will find all the ports and service versions of these ports with **“Nmap”**

![Nmap command](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*MjJCcaDCPq9KCDTxKEVkRg.png)

As we can see http port”**80**" is open therefore we can access its web interface by **http://<ip address>**

![web interface](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*oboFNtd259JzdkbSp9DHMQ.png)

I inspect the page to view the source code of page .Here I found an interesting thing in the comment section . username “**R1ckRul3s**” which will further assists us in the next enumeration phases

![username found](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*p6d3xvw7L9f_RbS-tnD7eg.png)

Then I started fuzzing directories with **gobuster** for the sake of expanding our reconnaissance phase . I found a directory “**/robots.txt**” which normally gives clue , secret in the ctfs.

In web, Robots.txt provides instructions to web instructions to web crawlers about which part of website they allowed to access or crawl.

![Directory Fuzzing](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Qkl3Jf2WGJHcV4Llfy823A.png)

when I navigate to “**/robots.txt** “ I found an interesting stuff , Probably its password of username that i found earlier

![password of username](https://miro.medium.com/v2/resize:fit:1254/format:webp/1*TM1vB6merUDDRGZq32UYmQ.png)

Hence we have a username & probably password then we have to navigate to login page for validating whether these credentials are right or wrong?Therefore again I started fuzzing directories , this time i used ffuf with php,txt extensions

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*TTOjIRUw4Po0K36HOasZQw.png)

here we go login page found

![found a login page](https://miro.medium.com/v2/resize:fit:1366/format:webp/1*NN0n1ryvtvPEIgCfj36PnQ.png)

Now when I entered credentials which I discovered earlier its gives me access to command panel where we can execute commands for discovering hidden flags.

![Command Panel](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*mehusZy01fac_oh-FLKVXA.png)![viewing files with” ls”](https://miro.medium.com/v2/resize:fit:864/format:webp/1*OBzRCg8-x-6gSyzQMrnUkA.png)![command disabled](https://miro.medium.com/v2/resize:fit:1184/format:webp/1*PIOiCiDFm8PgTvJ0nMna6g.png)

But when I try awk ‘{print}’ file its execute . and Boom we find out first flag🎉

![Flag1](https://miro.medium.com/v2/resize:fit:996/format:webp/1*wlvYf9kf-mtuIXUMI6jNbg.png)

For finding second flag w**e** used this command again **awk ‘{print}’ clue.txt** for seeing content of this file.

![clue.txt content](https://miro.medium.com/v2/resize:fit:1140/format:webp/1*rn_oinuZGLEYbFaBJ-BWAA.png)

it tell us find other files for other ingredient so we will navigate to the home directory first because it gives you roadmap from start what other files available for explore

with “**ls /home”** as we can see here is a file exit named “**rick”**

![ls /home](https://miro.medium.com/v2/resize:fit:1084/format:webp/1*1ETtpp7OkgNlAOkfrjSjpA.png)

after navigating to file rick we found a file name “second ingredients”

![ls /home/rick](https://miro.medium.com/v2/resize:fit:1044/format:webp/1*mapkxyjhb3iakpk8QCvPUw.png)

but here is space in a file name , therefore we will use backslash in between file name for the sake of reading the content of file

awk ‘{print}’ /home/rick/second\ ingredients
Second flag found🎉

![second flag](https://miro.medium.com/v2/resize:fit:960/format:webp/1*2mKuMOMLgAKY78hlhdQSkg.png)

Now for third flag , because we don’t have any clue remaining so there is high chance we have to get root privileges for retrieving third flag of this room

![Result of “whoami” command](https://miro.medium.com/v2/resize:fit:938/format:webp/1*9gF-rnxaAz9tKt_zvUAcdQ.png)

Current user is **www-data.**

we will start to escalate our privileges by first inspecting the sudo permissions
by **sudo -l**

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*jPRMqsDXbUDb2gqLaTTfrQ.png)

**(ALL) NOPASSWD:ALL** it shows that the user **www-data** can run any command as root without a password. That’s basically full root access.

Normally we all know that we run any command in linux terminal that required root privileges we have to give password after entering **sudo** in command
But here when I entered **sudo su** it does not required a password

so we can execute any command with **sudo**

![root directory](https://miro.medium.com/v2/resize:fit:962/format:webp/1*UMycYeUWYo0SE0La7NNEIA.png)

After navigating to the root directory via **sudo ls /root** we found a file 3rd.txt probably this file will gives us 3rd flag 🎉

![captionless image](https://miro.medium.com/v2/resize:fit:1022/format:webp/1*km7MqZ_b8KU78H1mL5GspA.png)

Now with **sudo** we will used **awk ‘{print}’<filename>**again .
(sudo awk ‘{print}’ /root/3rd.txt)

Here we go we found our last flag of this room

![3rd Flag](https://miro.medium.com/v2/resize:fit:832/format:webp/1*A3NjDmzVCreIC-JIBJ6AwA.png)

If you enjoyed this blog, don’t forget to **like** 👍 and **follow** for more content.

You can also follow me on [LinkedIn](https://www.linkedin.com/in/muhammad-ahsanijaz) or GitHub for more