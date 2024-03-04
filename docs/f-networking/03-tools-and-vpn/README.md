# Linux Tools

Command-line tools may seem nerdy, but they’re often faster and more resource-efficient than full-fledged GUI alternatives.

Whether you want to see your hardware specs and health, running processes, debug network issues, see weather reports, and so much more, you can do it all from the comfort of your chosen terminal emulator.

## tcpdump

tcpdump is the world’s premier network analysis tool—combining both power and simplicity into a single command-line interface.

First of all we need to find our main networkinface with the `ip a` command. Note that most of the time the default interface will be `eth0`.

### Breaking down the Tcpdump Command Line
The following command uses common parameters often seen when wielding the tcpdump scalpel.

```bash
sudo tcpdump -i eth0 -nn -s0 -v port 80
```

-i : Select interface that the capture is to take place on, this will often be an ethernet card or wireless adapter but could also be a vlan or something more unusual. Not always required if there is only one network adapter.
-nn : A single (n) will not resolve hostnames. A double (nn) will not resolve hostnames or ports. This is handy for not only viewing the IP / port numbers but also when capturing a large amount of data, as the name resolution will slow down the capture.
-s0 : Snap length, is the size of the packet to capture. -s0 will set the size to unlimited - use this if you want to capture all the traffic. Needed if you want to pull binaries / files from network traffic.
-v : Verbose, using (-v) or (-vv) increases the amount of detail shown in the output, often showing more protocol specific information.
port 80 : this is a common port filter to capture only traffic on port 80, that is of course usually HTTP.

### Display ASCII text
Adding -A to the command line will have the output include the ascii strings from the capture. This allows easy reading and the ability to parse the output using grep or other commands. Another option that shows both hexadecimal output and ASCII is the -X option.

```bash
sudo tcpdump -A -s0 port 80
```

### Capture on Protocol
Filter on UDP traffic. Another way to specify this is to use protocol 17 that is udp. These two commands will produce the same result. The equivalent of the tcp filter is protocol 6.

```bash
sudo tcpdump -i eth0 udp
sudo tcpdump -i eth0 proto 17
```

### Capture Hosts based on IP address
Using the host filter will capture traffic going to (destination) and from (source) the IP address.

```bash
sudo tcpdump -i eth0 host 10.10.1.1
```

Alternatively capture only packets going one way using src or dst.

```bash
sudo tcpdump -i eth0 dst 10.10.1.20
```

### Write a capture file
Writing a standard pcap file is a common command option. Writing a capture file to disk allows the file to be opened in Wireshark or other packet analysis tools.

```bash
tcpdump -i eth0 -s0 -w test.pcap
``` 

## Monitoring tools

Have you ever experienced slow application performance on a server and wondered which process was causing the bottleneck? In a production server environment, monitoring system performance and hardware resource usage in real-time is crucial. That’s where system monitoring tools come in handy. In this part we will discuss some nice tools to monitor your system

### top

`top` command is used to show the Linux processes. It provides a dynamic real-time view of the running system. Usually, this command shows the summary information of the system and the list of processes or threads which are currently managed by the Linux Kernel. As soon as you will run this command it will open an interactive command mode where the top half portion will contain the statistics of processes and resource usage. And Lower half contains a list of the currently running processes. Pressing q will simply exit the command mode.

To use top, simply enter `top` in the command line.


### btop

Meet btop, an aesthetically pleasing system resource monitor showing usage and stats for processor, memory, disks, network, and processes.

First of all install btop and explore the GUI like system monitor
```bash
sudo apt install btop
``` 

### du

`du` stands for `Disk Usage` and is a console command that displays the disk space used by files and directories. When you run the command in the console, it shows you the size of files and directories in bytes. This is especially useful if you want to determine which files and directories take up the most space on your system.

Showing the directories and files with their size by using `-hs` option. This output gives us in a human readable format (h) the summary (s) size of every folder/file 

```bash
du -hs
```

Give only the size of the current folder

```bash
du -hs .
```

Give only the size of the PDF files

```bash
du -hs *.pdf
```

Sort the output from big to small size

```bash
du -h * | sort -hr
```

Show the sizes of all files and folders in the current folder

```bash
du -h * --max-depth=1
```

Exclude files/folders from the output

```bash
du --exclude="*.zip*" -hs
```

::: disk free also known as `df`, which is a powerful utility that provides valuable information on disk space utilization. The df command displays information about file system disk space usage on the mounted file system. This command retrieves the information from `/proc/mounts` or `/etc/mtab`. By default, df command shows disk space in Kilobytes (KB) and uses the SI unit suffixes (e.g, M for megabytes, G for gigabytes) for clarity. :::

### ncdu

The ncdu command provides a fast and very easy-to-use way to see how you are using disk space on your Linux system. It allows you to navigate through your directories and files and review what file content is using up the most disk space.

```bash
sudo apt install ncdu
```

## Download Managers

### wget

Wget is the non-interactive network downloader which is used to download files from the server even when the user has not logged on to the system and it can work in the background without hindering the current process. 
 
GNU wget is a free utility for non-interactive download of files from the Web. It supports HTTP, HTTPS, and FTP protocols, as well as retrieval through HTTP proxies. 

To simply download a webpage 
 
```bash
wget http://example.com/sample.php
```

To download the file in background 
 
```bash
wget -b http://www.example.com/samplepage.php
```

To overwrite the log while of the wget command 
 
```bash
wget http://www.example.com/filename.txt -o /path/filename.txt
```

### curl

curl is a command-line tool to transfer data to or from a server, using any of the supported protocols (HTTP, FTP, IMAP, POP3, SCP, SFTP, SMTP, TFTP, TELNET, LDAP, or FILE). curl is powered by Libcurl. This tool is preferred for automation since it is designed to work without user interaction. curl can transfer multiple files at once. 

The most basic use of curl is typing the command followed by the URL.  

```bash
curl https://www.example.com
```

This should display the content of the URL on the terminal. The URL syntax is protocol dependent and multiple URLs can be written as sets like: 

```bash
curl http://site.{one, two, three}.com
```

URLs with numeric sequence series can be written as: 

```bash
curl ftp://ftp.example.com/file[1-20].jpeg
```

Progress Meter: curl displays a progress meter during use to indicate the transfer rate, amount of data transferred, time left, etc.
`-O` saves the files with the same name as the url. 
This equals to the `wget` command from above.

```bash
curl -# -O https://proof.ovh.net/files/100Mb.dat
```
