# A Brief History of CUPS
From https://openprinting.github.io/cups/
```
CUPS was originally developed by Michael R Sweet at Easy Software Products starting in 1997, with the first beta release on May 14, 1999. Not long after, 

Till Kamppeter started packaging CUPS for Mandrake Linux and created the Foomatic drivers for CUPS, leading the adoption of CUPS for printing on Linux. 

Apple licensed CUPS for macOS in 2002, and in February 2007 Apple purchased CUPS and hired Michael to continue its development as an open source project.


In December 2019, Michael left Apple to start Lakeside Robotics. In September 2020 he teamed up with the OpenPrinting developers to fork Apple CUPS to 

continue its development. Today Apple CUPS is the version of CUPS that is provided with macOS® and iOS® while OpenPrinting CUPS is the version of CUPS 

being further developed by OpenPrinting for all operating systems.
```

Summary,

CUPs: For apple OS

OpenPrinting CUPs: For Linux OS (Ubuntu, Debian, Armbian, ...)

So, We need to use OpenPrinting CUPs for Linux OS.

# Setup Thermal Printer Server (In Linux)
Prefer: https://pimylifeup.com/raspberry-pi-print-server/

## Install Thermal printer driver
### For Beeprt-BYXXX
https://www.beeprt.com/download/drivers/2.html

https://drive.google.com/drive/folders/1RovQ-Cr1pb36OcfGu5O9603DJ-jZFgll?usp=sharing

![image](https://user-images.githubusercontent.com/32226325/190885585-3f57bd29-fd5c-4d02-b1c2-d0b49301c83d.png)

### Install CUPs OpenPrintings

Setup build tools first

```
sudo apt-get install autoconf build-essential libavahi-client-dev \
     libgnutls28-dev libkrb5-dev libnss-mdns libpam-dev \
     libsystemd-dev libusb-1.0-0-dev zlib1g-dev
```

Clone OpenPrinting CUPs source code and build, and then, install to your system
```
git clone https://github.com/OpenPrinting/cups.git
cd cups
./configure
make -j 
sudo make install
```

lpadmin binary is located at /usr/sbin With Debian OS, we need to export /usr/sbin and /sbin to the PATH variable environments 

`vim ~/.bashrc`

`export PATH=$PATH:/usr/sbin:/sbin`

`source ~/.bashrc`

Add current user to lpadmin goup

```
sudo groupadd lpadmin
sudo usermod -a -G lpadmin $USER
```

Enable and start CUPs

```
sudo systemctl enable cups
sudo systemctl restart cups
```

Change `SystemGroup ...` to `SystemGroup sys root lpadmin`

<img width="651" alt="image" src="https://user-images.githubusercontent.com/32226325/193452256-0b5493cd-0105-4024-8511-9e991a649441.png">

`sudo systemctl restart cups`

Change CUPs config to support remote from any where in local network

```
sudo cupsctl --remote-any
sudo systemctl restart cups
```

If you can not use CUPs https link (https://localhost:631), this error from SSL/TLS cert
Change in cups-file.conf

`sudo vim /etc/cups/cups-files.conf`

<img width="389" alt="image" src="https://user-images.githubusercontent.com/32226325/193452241-209f0da7-3c15-453c-b2f2-d6d95ad26a89.png">

### Setup Printer server

Insert USB of your printer to driver first, the Linux PC will be setting up first

![image](https://user-images.githubusercontent.com/32226325/190887513-93fad9d0-8b2f-44af-b8dd-44c705bc22fc.png)

Open CUPs web browser: `http://localhost:631` -> Select printers tab -> Select to your USB printer

![image](https://user-images.githubusercontent.com/32226325/190887540-c4f84d6e-d6a7-46fa-9e5c-5ab1bc293aa5.png)

![image](https://user-images.githubusercontent.com/32226325/190887558-9c4982a7-2708-4424-8cce-8c23ea50dcaf.png)

Select Modify printer, wait for seconds

![image](https://user-images.githubusercontent.com/32226325/190887583-1e3599e3-3282-485a-917a-d3ca6ba0db43.png)

![image](https://user-images.githubusercontent.com/32226325/190887602-6835ee60-4a5f-4043-a112-334a43d03579.png)

Select continue

![image](https://user-images.githubusercontent.com/32226325/190887615-6b249cee-a2b9-4dfa-9d83-11caf9fcb662.png)

And continue

![image](https://user-images.githubusercontent.com/32226325/190887619-53862d4b-ba85-4546-8da1-aca55b60bde4.png)

If the printer model do not listed, please select PDD file from downloaded driver

![image](https://user-images.githubusercontent.com/32226325/190887664-3e24e890-f926-4213-b8e8-19b5881f0927.png)

![image](https://user-images.githubusercontent.com/32226325/190887672-34850644-7f45-435e-8695-dbc35de2c3bc.png)

Modify printer

![image](https://user-images.githubusercontent.com/32226325/190887686-9524076c-8be8-4183-8085-854b776861d0.png)

Successfully!

![image](https://user-images.githubusercontent.com/32226325/190887696-10ecbeeb-43fb-4d9b-8034-3afbc2e8f9c4.png)

Set default options: Go to printers -> your USB printer -> select default options -> Change something in here

![image](https://user-images.githubusercontent.com/32226325/190887735-b9199292-f07e-4a6d-8200-e6464bc69eff.png)

Sample, change media size to 74*105 mmm

Get size of pager: https://papersizes.io/a/a7

![image](https://user-images.githubusercontent.com/32226325/190887753-b9254bb2-b9a7-44dc-a7c3-15459f02dd25.png)


### Install Samba
Samba is a tool allow access data between Linux and other OS (Window, MAC)

Install:

`sudo apt install samba`

Change printer config:

`sudo vim /etc/samba/smb.conf`

Search and modify this config ([printers] and [print$]):

```
# CUPS printing.
[printers]
comment = All Printers
browseable = no
path = /var/spool/samba
printable = yes
guest ok = yes
read only = yes
create mask = 0700

# Windows clients look for this share name as a source of downloadable
# printer drivers
[print$]
comment = Printer Drivers
path = /var/lib/samba/printers
browseable = yes
read only = no
guest ok = no
```

Reload samba config:

`sudo smbcontrol all reload-config`

Restart samba server:

`sudo systemctl restart smbd`

# Setup thermal printer on the client PC (other OS like MAC or Windows)
## Window client
### Install printer driver (.inf file)
#### For beeprt BY-XXX
https://help.beeprt.com/support/solutions/articles/60000561059-install-beeprt-driver-on-windows

https://beeprt.freshdesk.com/helpdesk/attachments/60003204797

Install -> The driver (File BEEPRT.inf at location: `C:\Program Files\Beeprt\x64`

![image](https://user-images.githubusercontent.com/32226325/190890708-2f9cae37-f6fe-4808-9c92-586ebcbca044.png)

![image](https://user-images.githubusercontent.com/32226325/190890759-6eb0baf5-be39-4c4c-863d-0e56d36f16b3.png)

In this step, press exit

![image](https://user-images.githubusercontent.com/32226325/190890785-4df057c0-9aea-480d-b786-138969a71998.png)

### Add CUPs printer
Go to Networks -> Enter \\[printer_server_ip] in the textbox -> Enter -> The printer will be showed

![image](https://user-images.githubusercontent.com/32226325/190886095-b0611361-e971-44cf-8564-01eabe965073.png)

Go to printers & scanner -> Add printers & scanner

![image](https://user-images.githubusercontent.com/32226325/190886003-03db4b09-e53a-4058-bca7-1b344b0db8c8.png)

Select this printer that I want isn't listed -> Select a shared printer by name -> Enter \\[printer_server_ip]\[printer_name]

![image](https://user-images.githubusercontent.com/32226325/190886128-46ed4ced-bf3a-40dc-b8c2-13f88dae77ff.png)

Select Next -> No driver found. So, we need to setup driver to printer driver list.

![image](https://user-images.githubusercontent.com/32226325/190886157-ebf93d0a-4c11-4193-a8d8-6b07c965f1a6.png)

Select Have disk and point to file BEEPRT.inf

![image](https://user-images.githubusercontent.com/32226325/190886176-5ff37b87-e0b8-48e3-822e-0f2ec03dc5d6.png)

![image](https://user-images.githubusercontent.com/32226325/190886196-8a29b95d-8614-4ec6-a731-ff88e09b231c.png)

![image](https://user-images.githubusercontent.com/32226325/190890904-3005dc49-215f-4af6-921e-31117461c86d.png)

The beeprt printer driver will be available in list

Select "BY-426" for BY-368 printer (AYIN)

![image](https://user-images.githubusercontent.com/32226325/190890936-8ab770d6-3b02-4a1e-8881-88c6081a76f7.png)
 
Successfull added, You can test printer by "print a test page button" Enjoying...

![image](https://user-images.githubusercontent.com/32226325/190891003-ffc07f12-5495-4798-be05-cfa6cc016ac1.png)

![image](https://user-images.githubusercontent.com/32226325/190891011-7c80edf3-9b66-4d35-a4bf-f2f3d4ec338b.png)

# Use http adrress instead samba server printer
We can add a printer use CUPs printer address

![image](https://user-images.githubusercontent.com/32226325/190887214-474351e4-ead5-4d9f-9ed0-68dfd9ad1fbb.png)

# Label printer can not work well
We have a trouble with default label printer driver on Linux. So, we need update it.

Go to Cups web -> printers -> select the printer you want update driver -> Modify printer

# Set a Paper size for Window printer
Go to Printers & Scanner -> Select your printer -> manage

![image](https://user-images.githubusercontent.com/32226325/190896871-092ae796-5ba8-4233-baeb-001c6e0cc785.png)

Select Printer Property

![image](https://user-images.githubusercontent.com/32226325/190896892-7d719d44-1f89-48d9-a972-c9d850250f18.png)

Select Reference -> Page setup -> New

![image](https://user-images.githubusercontent.com/32226325/190896912-fd61828a-ed8d-4323-bbf1-d910c2f155db.png)

![image](https://user-images.githubusercontent.com/32226325/190896932-3367f0f3-0e1f-4aa5-b1c5-cc2d0672ce95.png)

Create new pager size -> Apply -> OK

![image](https://user-images.githubusercontent.com/32226325/190896956-4fa43c86-2659-48d1-89a8-d96c773e6a9e.png)

# Print on Window
Should use Adobe Reader tool to print page

Open PDF file with Adobe Reader -> `Ctrl + P`

Select Page setup:

![image](https://user-images.githubusercontent.com/32226325/190897015-4e1a5069-3875-4278-a6f6-b1a72653a5b6.png)

You can select Fit size options to fit PDF file with actual pager size

![image](https://user-images.githubusercontent.com/32226325/190897106-3d92f394-f95e-443a-9a83-170cc14bd110.png)

Or better, you should use the Custom scale (Note; to reload preview scaled, select to other mode (ex: Fit) and back to Custom Scale)

![image](https://user-images.githubusercontent.com/32226325/190897149-c5335bbf-a031-429f-a7ea-412534fd68d0.png)

# Setup Beeprt on Linux Arm (armv7 is 32 bit arm (Raspberry Pi 3) and armv8 is 64 bit arm (Orange Pi 3 LTS))
## Use Zebra CPCL Label Printer instead
Setup this driver is same with setup on Linux x86, but note that, the media size is: 4x5 inch

<img width="1437" alt="image" src="https://user-images.githubusercontent.com/32226325/193323133-40df7306-8408-4fe9-8c6b-6700e4c7fad9.png">

## Change A7 size map with printer size
<img width="777" alt="image" src="https://user-images.githubusercontent.com/32226325/191894646-d41f4756-6f44-48bd-9e34-9f5b6522d185.png">

`3.2x4.3 inch ~ 81x110 mm`

<img width="695" alt="image" src="https://user-images.githubusercontent.com/32226325/191894692-64f61785-37e1-4626-a1e6-4c2104f09ee7.png">
