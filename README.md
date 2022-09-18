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

Open CUPs web browser: `http://localhost:631`

![image](https://user-images.githubusercontent.com/32226325/190885611-6dbbbad2-d236-4ec0-b4e0-42b62de6c576.png)

![image](https://user-images.githubusercontent.com/32226325/190885627-b89b127a-f152-4f04-8569-00f4b9079074.png)

Select beeprt printer, and select shared printer

![image](https://user-images.githubusercontent.com/32226325/190885638-ce9ec2de-8e5b-42d0-910d-e3ad51391f07.png)

Select driver installed, and install printer

![image](https://user-images.githubusercontent.com/32226325/190885647-be467249-d00a-42ca-9dc2-424f59028eff.png)

Set Printer Options, select compatible media size -> set default options

![image](https://user-images.githubusercontent.com/32226325/190885705-797b33f7-79ae-4641-950f-cc288f004654.png)

![image](https://user-images.githubusercontent.com/32226325/190885727-bf66cda2-3d59-4830-bb04-d5b1bb34cf1b.png)


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
