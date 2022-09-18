# Setup Thermal Printer Server (In Linux)
Prefer: https://pimylifeup.com/raspberry-pi-print-server/

## Install Thermal printer driver
### For Beeprt-BYXXX
https://www.beeprt.com/download/drivers/2.html

https://drive.google.com/drive/folders/1RovQ-Cr1pb36OcfGu5O9603DJ-jZFgll?usp=sharing

![image](https://user-images.githubusercontent.com/32226325/190885585-3f57bd29-fd5c-4d02-b1c2-d0b49301c83d.png)

### Install CUPs
```
sudo apt update
sudo apt upgrade
sudo apt install cups
sudo usermod -a -G lpadmin $USER
sudo cupsctl --remote-any
sudo systemctl restart cups
```

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

Sample, change media size to 100x120 mmm

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
https://www.seagullscientific.com/downloads/printer-drivers/beeprt-by-368/

![image](https://user-images.githubusercontent.com/32226325/190885904-46fb944c-0120-4900-a8ce-98a77592cdfd.png)

![image](https://user-images.githubusercontent.com/32226325/190885914-8a173eb7-7418-4210-beca-275f35139ed2.png)

![image](https://user-images.githubusercontent.com/32226325/190885927-426a2218-f1fb-4b21-87e6-09c2229755f6.png)

In this step, select Cancel

![image](https://user-images.githubusercontent.com/32226325/190885948-3e5bf3bf-0225-401a-b988-54dd25f01f23.png)

### Add CUPs printer
Go to Networks -> Enter \\[printer_server_ip] in the textbox -> Enter -> The printer will be showed

![image](https://user-images.githubusercontent.com/32226325/190886095-b0611361-e971-44cf-8564-01eabe965073.png)

Go to printers & scanner -> Add printers & scanner

![image](https://user-images.githubusercontent.com/32226325/190886003-03db4b09-e53a-4058-bca7-1b344b0db8c8.png)

Select this printer that I want isn't listed -> Select a shared printer by name -> Enter \\[printer_server_ip]\[printer_name]

![image](https://user-images.githubusercontent.com/32226325/190886128-46ed4ced-bf3a-40dc-b8c2-13f88dae77ff.png)

Select Next -> No driver found. So, we need to setup driver to printer driver list.

![image](https://user-images.githubusercontent.com/32226325/190886157-ebf93d0a-4c11-4193-a8d8-6b07c965f1a6.png)

Select Have disk and point to file beeprt.inf

![image](https://user-images.githubusercontent.com/32226325/190886176-5ff37b87-e0b8-48e3-822e-0f2ec03dc5d6.png)

![image](https://user-images.githubusercontent.com/32226325/190886196-8a29b95d-8614-4ec6-a731-ff88e09b231c.png)

![image](https://user-images.githubusercontent.com/32226325/190886211-177d9e1d-973f-40c9-ad17-e788d3ecb459.png)

The beeprt printer driver will be available in list

Select "Beeprt Label printer 3 inch" for BY-368 printer (AYIN)

![image](https://user-images.githubusercontent.com/32226325/190886249-fa5aa2ea-e350-488e-ab85-a500ea426c34.png)
 
Successfull added, You can test printer by "print a test page button" Enjoying...

![image](https://user-images.githubusercontent.com/32226325/190886263-703f88ca-f9bb-47fe-b2c1-5f909239e28d.png)

![image](https://user-images.githubusercontent.com/32226325/190886283-1b520dcc-0171-441d-b7db-8bc5054a05cd.png)

# Use http adrress instead samba server printer
We can add a printer use CUPs printer address

![image](https://user-images.githubusercontent.com/32226325/190887214-474351e4-ead5-4d9f-9ed0-68dfd9ad1fbb.png)

# Label printer can not work well
We have a trouble with default label printer driver on Linux. So, we need update it.

Go to Cups web -> printers -> select the printer you want update driver -> Modify printer

