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








