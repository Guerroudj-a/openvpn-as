# OpenVPN Access Server Installation and Activation Guide

## Prerequisites
Make sure you have the following prerequisites before proceeding:
- Ubuntu 20.04 operating system
- Root access or sudo privileges

## Installation Steps

1. Update the system packages and install necessary dependencies alongside OPENVPN Access Server :
   ```bash
   
   # Update and upgrade the fresshly installed system
   apt update && apt upgrade
   
   # Setup Needed Binaries
   apt -y install ca-certificates wget net-tools gnupg unzip python3-pip
   
   # Add the Source repo for OVPN to APT And Install it
   wget https://as-repository.openvpn.net/as-repo-public.asc -qO /etc/apt/trusted.gpg.d/as-repository.asc
   echo "deb [arch=amd64 signed-by=/etc/apt/trusted.gpg.d/as-repository.asc] http://as-repository.openvpn.net/as/debian focal main" > /etc/apt/sources.list.d/openvpn-as-repo.list
   apt update && apt -y install openvpn-as=2.11.2-72c0e923-Ubuntu20
   
   # Hold Updates To prevent the system from overwriting the cracked Version Then Stop it
   apt-mark hold openvpn-as openvpn-as-bundled-clients
   service openvpnas stop
   ```
   
2. Activate OpenVPN-AS :
   ```bash
   
   # Make a directory on your local user home directory and Enter It
   mkdir ~/vpnas
   
   # Copy The egg file to it
   # The file name misht change depending on the python version you are using pyovpn-2.0-pyX.X.egg
   cp /usr/local/openvpn_as/lib/python/pyovpn-2.0-py3.8.egg .
   
   # Uncompress The egg file
   mv pyovpn-2.0-py3.8.egg pyovpn-2.0-py3.8.egg.zip
   unzip pyovpn-2.0-py3.8.egg.zip

   # Change Directory to where the targeted Compiled pyc file is
   cd ./pyovpn/lic
   
   # Install uncompyle6 python library
   pip install uncompyle6
   
   # Decompile the uprop.pyc File
   uncompyle6 uprop.pyc > uprop.py
   ```
   ![Original uprop.py](assets/uprop.pyc.orig.png)
   ```bash
   # Edit the decomplied file by adding 
   # ret['concurrent_connections'] = 5000
   # just before the ret statement in the figure function
   # with the same indentation as the ret statement and after the line
   # print "ret['%s'] = v_agg(%d) + v_nonagg(%d)" % (prop, v_agg, v_nonagg)
   vi uprop.py
   ```
   ![Modefied uprop.py](assets/uprop.pyc.cracked.png)
   ```bash
   # Recompile the file and copy it to the corrent path
   python3 -m py_compile uprop.py
   # Make sure to check the Version as it may change depending on python
   cp __pycache__/uprop.cpython-38.pyc uprop.pyc
   
   # Clean the code
   rm -Rf __pycache__/ uprop.py

   # Go to the base directory in this case ~/vpnas
   cd ~/vpnas

   # Remove the egg file we renamed to zip
   rm pyovpn-2.0-py3.8.egg.zip 
   
   # Recreate a zip file and rename it to the original .egg filename
   zip -r *
   # In my case my generated file is named EGG-INFO.zip
   cp EGG-INFO.zip /usr/local/openvpn_as/lib/python/pyovpn-2.0-py3.8.egg
   
   # Clear the openvpnas service log file
   echo "" > /var/log/openvpnas.log

   # start the service and you are done
   service openvpnas start
   ```

2. Activate OpenVPN-AS SSL and configure a sub-domain :

   [Set up you own VPN Server with OPENVPN Access Server!](https://www.youtube.com/watch?v=p3HszAtNu-s)

### Contributing ###
Contributions are welcome! Feel free to submit issues, feature requests, or pull requests to enhance this OpenVPN Access Server installation repository.

### Disclaimer ###
This repository is intended for ==educational and informational purposes only==. The cracking steps are provided for customization purposes, and users should ensure compliance with applicable laws and regulations.

Note: Always use software responsibly and adhere to licensing agreements.[^1]

[^1]:Enjoy secure and customizable VPN deployment with OpenVPN Access Server on Ubuntu 20.04!
