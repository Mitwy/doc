[![T0_P3](https://img.youtube.com/vi/gCRiLKlUEa0/0.jpg)](https://www.youtube.com/watch?v=gCRiLKlUEa0)
###  How to SSH from windows to linux using PUTTY with key | VPS Beginner | P3
**Enable insert in vim over ssh**

```
vim /usr/share/vim/vim82/defaults.vim
" In many terminal emulators the mouse works just fine.  By enabling it you
" can position the cursor, Visually select and scroll with the mouse.
"if has('mouse')
"  if &term =~ 'xterm'
"    set mouse=a
"  else
"    set mouse=nvi
"  endif
"endif
```

**Create User**  
```
adduser <userName>
passwd <userName>
<psw> 
chsh -s /bin/bash <userName>
```

**Add user to sudo**  
```
usermod -aG sudo <userName>
```

**Generate keys**    
Use putty (!!! Make sur putty is up to date) or linux with [ssh-keygen](https://www.server-world.info/en/note?os=Ubuntu_23.04&p=ssh&f=4)

**Create directory and set up public key**  
```
# Loged in as <userName>
mkdir -p ~/.ssh
cd ~/.ssh
vim ~/.ssh/authorized_keys
chmod 644 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chown <userName>:<userName> ~/.ssh/authorized_keys
```

**Test ssh auth**  
Using Putty or with linux cmd:
```
# id_rsa contains private key generated with ssh-keygen
ssh -i ~/.ssh/id_rsa <userName>@<serve_ip>
```

**Force ssh key authentification**  
```
# Make sure /etc/ssh/sshd_config does not include any other configuration.
vim /etc/ssh/sshd_config
# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no
```
**upload files**  
```
path=%path%;C:\Program Files (x86)\PuTTY
pscp -P 22 -r File_To_Upload_Path User_login@Ip_server:Path_On_Server
TIMEOUT 50
```
