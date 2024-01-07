## Challenge 1 - Gain Access to a Remote Server

was expecting to gain root privilege and then there sshould be something in `/root` folder so started by downloading and booting into the vm with virtualbox and now I logged in as guest user.

ran `ss -tunl` to know all open ports and found a http server on port 80, a mysql server at 3306 and a CUPS server at 631.

now opened the website in the vm itslef it was about xenia and now I wanted to open it on my host so went on localhost and it didnt worked, checked the network adapter was bridge and thought this should work. so just used firefox inside the vm and had a glance at the website and `localhost/robots.txt` and found some paths but none were suspicious ecept `/nothing` but didnt find anythin useful.

Now I wanted to look at it on host for better dev tools, so as a jugaad used nat adapter with port forwarding and checked for cookies, localstorage and headers but stoped looking on website.

Shifted my focus on CUPS since it was old ran a searchsploit on cups and found remote code execution which was very ccomplex but kept on looking then found that we can change the location of err file and thought of changing it to `.bash_history` or even `root/flag.txt` and then read it via web dashboard of cups but idk why but changing the err file required root privilege so it didnt worked and then gave up on it.

then after meet call in morning understood that bridge actually assigns a different IP to vm as well so did `ip addr` and now used that IP and it worked.

again went on `VM_IP/robots.txt` to see all the possible routes and tried each this time also inspecting it,an suprisingly `/nothing` had `#my secret pass` as

```
xenia
tux
freedom
password
diana
helloworld!
iloveroot
```

commented in html. tried using this as loggin into root with su root but it didn't worked. so saw all the available user by `cat /etc/passwd` and tried for every user even tried encoding the pass with base64 and used that but it didnt worked.
