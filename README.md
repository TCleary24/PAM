# PAM

# Project Description
completely discuss what PAM is
and fully explain your project and the modifications your made. A reader should be able to duplicate your
work directly by following your shared space. Graphics and images are very important to include. 

Description of PAM: Pluggable Authentication Modules (PAM) is a xxxxxxx.

This project improves system security by establishing two-factor authentication requiring a user to not only know a password, but also have a removable USB device, in order to authenticate.

# Prerequisites

Kali Linux base install on a virtual machine
Debian
USB device
No other deviations to pam.d files (could cause fatal error in step 2 below.
Others?xxxx

# Steps

Install the pam_usb prerequisites by typing the following command <br />
 <br />
  $ sudo apt install git libxml2-dev libpam0g-dev libudisks2-dev libglib2.0-dev gir1.2-udisks-2.0 python3 python3-gi
 
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/prereq%20installation.png)
  
  
Clone the pam_usb GitHub repository and compile the code to install it with the following commands <br />
  $ git clone https://github.com/mcdope/pam_usb.git <br />
  $ cd pam_usb/ <br />
  $ make <br />
  $ sudo make install <br />
  
   ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/github%20compile.png)
  
Add the usb device intended for authentication with the following command, where "USB20FD" is the name of the removable device <br />
  $ sudo pamusb-conf --add-device USB20FD
  
  ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/add%20device.png)
 
Save the changes to the file <br />
  $ Y
  
Next, define a user for PAM authentication with the following command, where "tim" is the name of a user that exists on the system <br />
  $ sudo pamusb-conf --add-user tim
  
  ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/add%20user.png)
  
 Save the changes to the file <br />
  $ Y
  
 At this point, PAM files have been edited to enable the USB device to replace the need for a password.  A user can still login as before with their password, but now if the USB is detected a password is no longer required.  The next step will adjust a module in the common-auth file in /etc/pam.d to require both USB and password for successful logins.
 
Go to the PAM directory <br />
  $ sudo cd /etc/pam.d
    
Open the common-auth file <br />
  $ nano common-auth
  
![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/edit%20pamd.png)
  
Change the pam_usb.so module control flag from "sufficient" to "required" <br />
  $ auth  required  pam_usb.so
 
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/common_auth_initial.png)
 
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/common_auth_final.png)
 
 
Save the changes <br />
  $ Y

# Conclusion
Now, login attempts will fail when the USB is not inserted into the device.
