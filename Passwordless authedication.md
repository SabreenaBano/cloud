Step 1: Launch Two EC2 Instances
Step 2: Create a User on Server1
         1.SSH into Server1 using the key pair you downloaded when launching the instance:
                ssh -i /path/to/key.pem ec2-user@<server1_public_ip>
         2.Create a new user called user1
                  sudo adduser user1
         3.Set a password for user2 (optional):
                  sudo passwd user2
         4 Switch to user2:  
                   sudo su - user2
Step 3: Generate RSA Key Pair on Server1
          ssh-keygen -t rsa
Step 4: Create a User on Server2
              ssh -i /path/to/key.pem ec2-user@<server2_public_ip>
              sudo adduser user2
              sudo passwd user2
              sudo su - user2
Step 5: Prepare .ssh Directory on Server2
          1 Create a .ssh directory for user2:
               mkdir ~/.ssh
          2.Set proper permissions for the .ssh directory:
               chmod 700 ~/.ssh
          3.Create an authorized_keys file inside .ssh/:
               touch ~/.ssh/authorized_keys
          4.Set proper permissions for the authorized_keys file:
          chmod 600 ~/.ssh/authorized_keys
Step 6: Copy the Public Key from Server1 to Server2
         1. On Server1, as user1, display the contents of the id_rsa.pub file:
            cat ~/.ssh/id_rsa.pub
         2.Copy the entire content of the id_rsa.pub file.
         3.On Server2, as user2, open the authorized_keys file in an editor:
            nano ~/.ssh/authorized_keys
         4.Paste the copied public key into this file and save it.
Step 7: SSH into Server2 from Server1
         1.On Server1, as user1, SSH into Server2 using the private IP of Server2:
           ssh user2@<server2_private_ip>
     should be able to log in to Server2 as user2 without a password.

                    

































#PasswordAuthentication yes

PasswordAuthentication yes

# Uncomment the following line and change it to permit root login (not recommended for security reasons)
# PermitRootLogin prohibit-password

PermitRootLogin yes

Save the changes and exit the text editor.



   