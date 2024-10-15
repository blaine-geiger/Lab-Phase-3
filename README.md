# Lab Phase 3: Network functionality and performance testing

### Private IP Address Ranges
Private IP addresses are not routable on the public internet and can be used within private networks. The standard ranges for private IP addresses are:
- 10.0.0.0 to 10.255.255.255
- 172.16.0.0 to 172.31.255.255
- 192.168.0.0 to 192.168.255.255
  
#### For the sake of clarity we will use the following examples:
  - 192.168.1.0 /24 for subnet 1
  - 192.168.2.0 /24 for subnet 2

> **Note:** All device IP addressing is used as an example and does not reflect the actual addressing scheme used in my network setup. These are given as placeholder IP addresses for
> clarity to the reader.

## Basic Network Functionality Testing

Here we will test basic network communication between all devices, including existing devices and those that have been set up and configured in the previous steps. Using a simple command line interface (CLI) tool called ‘nmap’ we will determine if a host is working, also referred to as ‘up’. This proves that communication is taking place. A faster tool called ‘ping’ could be used, but all the devices are set to ignore incoming pings and not send out replies. This is for security reasons and helps devices from being easily discovered by outside agents. Blocking pings doesn’t prevent attackers from discovering your hosts, but it does add obscurity because ping sweeps are often one of the first steps in gathering information about a possible target.

1. Right click the Windows start button on the taskbar.
2. Click ‘Terminal’:
    - This opens a terminal window, command prompt, or command line interface (CLI). It may be referred to by different names.
3. Click inside the window with the mouse.
4. Type `nmap <IP address>` without the quotes:
    - Replace `<IP address>` with the IP of each device you want to test.

The command line input and resulting output can be seen in the list below. The last two lines are important. This shows what IP the result is for, if the host is ‘up’, and the length of the latency. This is the round-trip time (RTT). Meaning how long it took for packets to be sent to the destination and return to the nmap source.

### Subnet 1 example
  - `C:\Windows\System32>nmap 192.168.1.0 /24` returns:
- FIREWALL LAN PORT: HOME GATEWAY 
- HOME ROUTER
- PC1
- PC2
- LAPTOP

### Subnet 2 example
  - `C:\Windows\System32>nmap 192.168.2.0 /24` returns:
- FIREWALL OPT PORT - LAB GATEWAY
- MANAGED SWITCH
- LAB PC
  
## Research Network Performance Analysis Tools

First, our internet connection speed will be tested. These speed statistics are measured in download, upload, and ping. The following testing servers are some of the most popular choices for testing internet connection speeds.

<p align="center">Strengths, weaknesses, and features of speed test providers.
  <br/>
  <img src="https://imgur.com/qn5ndyc.png" height="80%" width="80%" alt="Table1.1"/><br /><br />
</p>

- **Ookla** – A powerful and popular option that has servers all over the world (some others may not and are limited depending on your region). Very good for general performance testing.
- **Cloudflare** – Useful for checking connection performance to Cloudflare’s network. This is especially important if you are using Cloudflare services.
- **Fast.com** – Analyzes connection speed and performance to Netflix servers specifically. This can be useful information for how other streaming services will perform on the network.
- **LibreSpeed** – Open-source option that is highly customizable. Provides privacy advantages and is beneficial for those that want to have more control over their testing environment.

### iPerf3

iPerf3 is another network performance testing tool that focuses on the bandwidth, throughput, and latency between two devices on the same network. It is a powerful and flexible tool. Full documentation can be found at [here](https://iperf.fr/iperf-doc.php#3doc). Use and documented results of both internet speed testing and network performance testing can be found in the next section.

## Network Performance Testing

Internet speed testing is a straightforward process. Simply traveling to the associated website and clicking the ‘GO’ button will begin the testing. It should be noted that you should close as many applications as possible before beginning the testing (especially applications that use an internet connection like Spotify, Chrome, or other web browsers). 

We will use two in our testing; both Ookla and LibreSpeed. 

<p align="center">The results from the speed tests.
  <br/>
  <img src="https://imgur.com/EWfthxJ.png" height="80%" width="80%" alt="Table1.1"/><br /><br />
</p>

These are comparable results for either device being used, whether on ethernet or wireless, which are both on separate subnets. This reflects no large discrepancies between the choice of using ethernet or wireless and connecting to subnet 1 (home LAN) or subnet 2 (lab LAN).

While these speeds are not as fast as some top internet access speeds, they perform well for our purposes. I believe we are somewhat limited by the performance of the firewall, which has variable reports on throughput that are compatible with these speeds. We should also consider the replacement of some runs of ethernet cabling that was pre-existing on the network before we began this project. 


<p align="center">Averaging these together, we get results for our two devices listed in Mbps.
  <br/>
  <img src="https://imgur.com/tvTpjPk.png" height="80%" width="80%" alt="Table1.1"/><br /><br />
</p>

## LAN Performance Testing with iPerf3

Moving on to the LAN performance between devices, we now use iPerf3. We are only testing two devices that exist in the lab currently: our Lab PC and the Laptop.

1. Go to [this address](https://iperf.fr).
2. Click on the **Download iPerf binaries** tab.
3. Two links are available; we will use the GitHub link. Click on the link.
4. On the right side of the page under **Releases**, click on the green **latest tag**.
5. Click on the link to download the zip file.
6. Unzip this file.
7. Enter the unzipped folder.
8. While in the folder, click the address bar:
   - Type `cmd` in the address bar and press Enter.
9. This will open a command prompt window
10. Type `iperf3.exe -s -p 6000` and press Enter:
   - This creates a server (or sender) on port 6000, sending messages used for measuring performance.
   - Next, we will set up the receiver of these messages on another device. But first, we need the IP address of the sender machine, so:
11. Open a new terminal window and type `ipconfig` and press Enter.
12. Look for an entry that is named **Ethernet** or **Ethernet adapter**.
13. Note this **IPv4 address** down or leave the window open for now.
14. On the second device (Lab PC), open a terminal window:
   - Follow the same steps of downloading iPerf3, unzipping the file, entering the unzipped folder, and typing `cmd` in the address bar and pressing Enter.
15. Type `iperf3.exe -c <IP address> -p 6000` and press Enter in the newly opened terminal window (on the second device – the receiver – which is the Lab PC).
   - Allow the testing to run until it completes, which will only take a matter of seconds.

The iPerf3 program performs many small transfers of UDP packets to test performance. At the bottom right of the test results the bitrate of 949 Mbits/sec is seen. This is very close, but just less than, a benchmark of 1Gbs for local network speeds between devices within the lab subnet. These speeds are adequate for our purposes.
