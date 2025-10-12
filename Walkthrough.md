# Jangow-1 CTF by Vulnhub walkthrough

# Tools used

1. Netdiscover
2. Nmap

# First key

## Step 1 : Enumeration

First, we could use netdiscover or Nmap to find the IP address of the target machine.

Netdiscover could be used using `sudo netdiscover` while nmap could be used using `nmap -sS -T4 10.38.1.110-120`
NOTE : Using nmap as illustrated will only work if the setup process was the same as mine (https://github.com/kxnja/Jangow-01-CTF-by-Vulnhub/blob/main/setup.md)

Netdiscover yields the following;

<img width="955" height="277" alt="image" src="https://github.com/user-attachments/assets/b476db0b-30d4-4128-a165-a21c03640576" />

Nmap on the other hand gives us the following;

<img width="955" height="325" alt="image" src="https://github.com/user-attachments/assets/2c6b6efe-c842-4691-9972-04fb0a3a4d0f" />

Nmap not only gives the IP address of the target machine but also the open ports.

We find 2 open ports; port 21 and port 80.

Using your browser head on to `http://10.38.1.115` this takes us to a grayscale site.

## Step 2 : Foothold

On the top right corner of the website we find a `buscar` button.

<img width="1920" height="858" alt="image" src="https://github.com/user-attachments/assets/ee4f8b95-d85a-49d0-8ba1-27a39596b831" />

The website seems to be doing some kind of post request and might be vulnerable to Local file inclusion.
`Local file inclusion` is a vulnerability that lets a user load file from a server without proper validation.

When we do some research on buscar we find that it is a spanish word for search. We can the try to search for files on the search bar using the `ls` command.
`http://10.38.1.115/site/busque.php?buscar=ls -al` this give a wordpress file and when we `ls` the wordpress file `http://10.38.1.115/site/busque.php?buscar=ls -al wordpress`.

<img width="1920" height="357" alt="image" src="https://github.com/user-attachments/assets/e719e43e-2f97-4b86-9bc7-8e596d7a92a9" />

Under the wordpress file we find a config.php file that we can open using the `cat` command. `http://10.38.1.115/site/busque.php?buscar=ls -al wordpress/config.php`.

<img width="803" height="234" alt="image" src="https://github.com/user-attachments/assets/8dc089e6-8b5c-4bed-8614-0464056128ae" />

<img width="931" height="437" alt="image" src="https://github.com/user-attachments/assets/d3e5f255-7ee4-422b-953f-6f14ae0ceb05" />

We find a username and a password in the config.php file.

Copy and paste the code to a text editor. 
Open the terminal try create an ftp connection with the target machine using the credentials provided.

<img width="472" height="182" alt="image" src="https://github.com/user-attachments/assets/60a57e84-2ced-4415-b073-06ef10262886" />

The connection fails due to invalid credentials so we need to continue looking for the legitimate credentials.

Go back to your browser and using the `pwd` command we can print the working directory, which we find to be `/var/www/hrmal/site`.

<img width="955" height="232" alt="image" src="https://github.com/user-attachments/assets/7b451e6c-dbcc-4f38-bcc6-2cecfd6a906e" />

Using `ls -al /var/www/html` we can list all the directories and file in the directory.

<img width="948" height="206" alt="image" src="https://github.com/user-attachments/assets/7a966a31-a084-4484-ba6f-5b051b35bb6e" />

Here, we find a backip file that we can then open using `cat`.

<img width="958" height="362" alt="image" src="https://github.com/user-attachments/assets/2eb4a66f-c546-44dd-a96f-11f05af83b15" />

We find another username and password that we can then use to complete the ftp connection.

<img width="441" height="196" alt="image" src="https://github.com/user-attachments/assets/8efa9cf1-5bb6-4bea-96ea-7363b4394ad7" />

Upon trying the new set of credentials we successfully create a ftp connection.

<img width="514" height="173" alt="image" src="https://github.com/user-attachments/assets/011f7c69-eac1-45d2-81b4-462c9dcbe5d7" />

Change the directory to `/home` and using `ls` we find a `jangow01` directory and switching to it we find a `user.txt` file that we can download to our machine using the `get` command.

<img width="955" height="390" alt="image" src="https://github.com/user-attachments/assets/87962d16-ab65-4615-848c-7384a189fb5c" />

Using `ls` we can see the file on our machine and we can open it using `cat`.

<img width="480" height="65" alt="image" src="https://github.com/user-attachments/assets/968b9981-3e83-48d6-88c0-0b68d65843ca" />

## Step 3 : Privilege Escalation

First we need to set up a reverse shell. There are several methods of doing this but since we are aware of the LFI (Local file inclusion) vulnerability we can add a php file that will enable remote connection.

We can either write the php code on our on or get it on the internet.

I got mine from the internet ` https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php `.
Copy the code and using nano on the terminal save the file in a php file






