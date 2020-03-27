---
title: Tips.Copying-SSH-Keys-to-Servers
date: 2020-03-18 00:16:22
categories: 
- routine
tags: 
- ssh
---

# Tips for Copying SSH keys to Servers

> While remotely connecting servers via `ssh` protocol, I bet, many of us would get as annoyed as like me by the prompt to type in password for verification.

To be frank, those mechanism of this do is good for better safety, however, this also disburbs me a lot, especially for those who often access remote machine over ssh on their PCs. In that case, there is NO NEED to waste so much time and energy on such an unnecessary step.

Hence, how do we directly access our remote servers without any keys type-in via ssh?

---

 <!--more-->

HERE IS THE SOLUTION.

Just **ONE COMMAND LINE** needed on the first-time access to remote servers with keys.

> ssh-copy-id user@hostname -p port 



 Wait a minute then. Your ssh-keygen public rsa will be copyed to and saved on remote server `.ssh/known_hosts`. 



 Time to enjoy this direct and effortless access later.