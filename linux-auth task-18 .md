Linux Authenction (Task-19)
SSH (Secure Shell) is a widely used protocol for secure remote access to systems over a network. By default, SSH on Ubuntu may be configured to use key-based authentication, which provides a higher level of security compared to password authentication. However, in certain situations, such as for convenience or compatibility reasons, you may need to enable password authentication for SSH.
Prerequisites
Before you begin, ensure that you have:

Access to a terminal session on your Ubuntu 22.04 system.
Sudo privileges or access to the root account.
Step 1: Install OpenSSH Server (if not already installed)
If you haven’t installed the OpenSSH server on your Ubuntu system, you can do so by running the following command in your terminal:

$ sudo apt update
$ sudo apt install openssh-server

This will install the OpenSSH server package, which allows your system to accept SSH connections.
Step 2: Edit SSH Configuration
Next, you’ll need to edit the SSH server configuration file to allow password authentication. Open the SSH configuration file in a text editor using the following command:

$ sudo nano /etc/ssh/sshd_config
Find the following lines and make the necessary changes:

# Comment the following line (add # at the beginning)
Include /etc/ssh/sshd_config.d/*.conf

#Include /etc/ssh/sshd_config.d/*.conf

# Change ‘PasswordAuthentication’ to ‘yes’ to enable password-based
authentication
Step 3: Restart SSH Service
After making changes to the SSH configuration, you’ll need to restart the SSH service for the changes to take effect. Run the following command to restart the SSH service:
$ sudo systemctl restart ssh
Step 4: Testing SSH Login:
You can now test SSH login using a username and password, including the root user if permitted. Replace username with your actual username or root for the root user.

$ ssh username@your_server_ip
You will be prompted to enter the password for the specified user.
Subtask: Login as Root User:
Step 1: Set Password for the Root User
       sudo passwd root
Step 2: Allow Login as Root with Password
This was covered in Step 6 when you set PermitRootLogin yes in the SSH configuration file.
Final Output
You should now be able to log in as the root user by entering:
ssh root@your-instance-public-dns





























#PasswordAuthentication yes

PasswordAuthentication yes

# Uncomment the following line and change it to permit root login (not recommended for security reasons)
# PermitRootLogin prohibit-password

PermitRootLogin yes

Save the changes and exit the text editor.



   