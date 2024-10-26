# Raspberry Pi Print Server
Some documentation to support a quick and easy setup of a Raspberry Pi print server. This was a weekend/one day project to provide a print server for a USB printer that lacs modern networking for multi-user use.

## Setup Steps
### Basic Setup
1. Install Raspbian OS on a Raspberry Pi. I used the Raspberry Pi Imager to install the OS on a 32GB microSD card. link: https://www.raspberrypi.org/software/. Make sure to enable SSH, set up the WiFi connection and configure a user in the Raspberry Pi Imager settings.

2. (Optional) Update the Raspberry Pi OS. Run the following commands in the terminal:
```bash
sudo apt-get update
sudo apt-get upgrade
```

3. Cups should come preinstalled with recent images. If it does not install the CUPS print server and avahi daemon. Run the following command in the terminal:
```bash
sudo apt-get install cups avahi-daemon
```

4. Add the user to the lpadmin group. Run the following command in the terminal:
```bash
sudo usermod -a -G lpadmin pi
```

(if you are not using the default user, replace pi with the username you are using)

5. Configure CUPS to allow remote access. Edit the cupsd.conf file. Run the following command in the terminal:
```bash
sudo cupsctl --remote-any
sudo systemctl restart cups
```

6. Open a web browser and navigate to the CUPS web interface. Enter the following address in the address bar:
```bash
http://<raspberrypi_ip>:631
```

(if you dont see anything make sure the CUPS service is running by running the following command in the terminal):
```bash
hostname -I
sudo systemctl status cups
```
### Windows Setup
1. For Windows to see the print server you need to share it using the Samba service. Run the following commands in the terminal:
```bash
sudo apt-get install samba
```

2. Edit the smb.conf file. Run the following command in the terminal:
```bash
sudo nano /etc/samba/smb.conf
```

3. Adjust the file to look something like this. These entries should already exist the just need to be adjusted:
```bash
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
(Something to note this is being shared as a guest, if you want to require a password you will need to set up a user in Samba!)

4. Restart the Samba service. Run the following command in the terminal:
```bash
sudo systemctl restart smbd
```

### Adding a Printer
1. Open the CUPS web interface in a web browser. Enter the following address in the address bar:
```bash
http://<raspberrypi_ip>:631
```

2. Click on the Administration tab and then click on Add Printer.

3. Enter the username and password for the Raspberry Pi user with CUPS admin access.

4. Select the printer from the list of discovered printers and click Continue.

5. Select the correct printer modal (CUPS will try to figure this out on its own), configure the printer settings, make sure to check "Share this Printer" and click Add Printer.

6. The printer should now be added to the CUPS print server.