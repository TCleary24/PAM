# PAM

# Project Description

Pluggable Authentication Modules (PAM) are a feature in Linux that function as an API that authenticates services and users on a system.  This allows the rules for authentication to be dynamically configurable, allowing for a system administrator to define how users and applications authenticate within the Linux system. <br />
<br />
PAM functions with configuration files located in /etc/pam.d on the Debian release of Linux.  There is also a configuration file that is present, but is ignored in the presence of the PAM directory.  Authentication occurs by passing through a series of stacked modules.  The modules are run from top to bottom and a response is generated as pass or fail depending on the result of the series of rules defined in the modules. <br />
<br />
This project improves system security by establishing two-factor authentication requiring a user to not only know a password, but also have a removable USB device, in order to authenticate.  It leverages code that adjusts the PAM common-auth configuration file by installing a new USB module and reconfiguring the control flag to require a USB in addition to a password. <br />
<br />
The following steps were used to establish the necessary configuration implementing 2FA using a USB.  Note that changes to PAM files should be made with great care as errors could result in a user being completely locked out of a system.

# Prerequisites
VMWare: https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html <br />
Kali Linux base install using Debian OS: https://www.kali.org/get-kali/#kali-bare-metal <br />
Removable USB device <br />
No prior changes to pam.d files (could cause fatal error during new module compilation below) <br />


# Steps

Install the pam_usb prerequisites by typing the following command: <br />
 <br />
  $ sudo apt install git libxml2-dev libpam0g-dev libudisks2-dev libglib2.0-dev gir1.2-udisks-2.0 python3 python3-gi
  <br />
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/prereq%20installation.png)
  
   <br />
Clone the pam_usb GitHub repository and compile the code to install it with the following commands: <br />
  $ git clone https://github.com/mcdope/pam_usb.git <br />
  $ cd pam_usb/ <br />
  $ make <br />
  $ sudo make install <br />
  
   ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/install%20screen.png) <br />
  
Add the usb device intended for authentication with the following command, where "USB20FD" is the name of the removable device: <br />
  $ sudo pamusb-conf --add-device USB20FD <br />
  
  ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/add%20device.png)
  <br />
   
Note: The device name can be found on the desktop after the device connection is established. <br />

![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/device%20name%20homescreen.png)<br />

Save the changes to the file <br />
  $ Y
   <br />
Next, define a user for PAM authentication with the following command, where "tim" is the name of a user that exists on the system: <br />
  $ sudo pamusb-conf --add-user tim
  
  ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/add%20user.png)
  
 Save the changes to the file: <br />
  $ Y
  
 At this point, PAM files have been edited to enable the USB device to replace the need for a password.  A user can still login as before with their password, but now if the USB is detected a password is no longer required.  The next step will adjust a module in the common-auth file in /etc/pam.d to require both USB and password for successful logins. <br />
 
Go to the PAM directory: <br />
  $ sudo cd /etc/pam.d
    
Open the common-auth file: <br />
  $ nano common-auth
  
![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/edit%20pamd.png)
  
Change the pam_usb.so module control flag from "sufficient" to "required": <br />
  $ auth  required  pam_usb.so
 
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/common_auth_initial.png)
 
 ![alt text](https://github.com/TCleary24/PAM_USB_2FA/blob/main/common_auth_final.png)
 
 
Exit and save the changes: <br />
  $ Y

# Conclusion
Now, login attempts will fail when the USB is not inserted into the device.  These settings can be further customized to establish authentication requirements by adjusting the control flags.  For example, the pam_usb.so module could be given the "sufficient" control flag that would allow login immediately in the presence of the defined USB, without requiring a password.
