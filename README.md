# Linux Server Configuration

# Overview
A secure and efficient bare-bones Ubuntu Linux web & database server that hosts a Flask web application.

## Task 1 - Setup Ubuntu Linux Server on Amazon Lightsail

Local IP address: http://34.208.16.148

Web app: http://ec2-34-208-16-148.us-west-2.compute.amazonaws.com

## Task 2 - SSH into server

1. Download private key from
https://lightsail.aws.amazon.com/ls/webapp/account/keys

2. Move private key to ```~/.ssh``` directory:

   ```mv ~/Downloads/LightsailPrivateKey.pem ~/.ssh/```

3. Change permissions so that only owner can read and write:

   ```chmod 600 ~/.ssh/LightsailPrivateKey.pem```

4. SSH into server

   ```ssh -i ~/.ssh/LightsailPrivateKey.pem ubuntu@34.208.16.148```

## Task 3 - Create user grader and grant grader permission to sudo

1. Create new user grader

   ``` sudo adduser grader ```

2. Open and edit sudoers file

   ``` sudo visudo ```

3. Add ``` grader ALL=(ALL:ALL) ALL ``` below ``` root ALL=(ALL:ALL) ALL ```

4. Save edit by entering ``` ctrl-x ```, ``` y ```, and then ``` return ```

   Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps)

## Task 4 - Create a SSH key pair for grader

1. Switch to user grader

   ``` su - grader ```

2. ``` cd ``` into ``` /home/grader ```

3. Create a new directory

   ``` sudo mkdir .ssh ```

4. Create an authorized_keys file inside .ssh directory

   ``` sudo touch authorized_keys ```

5.  Generate keys using ``` ssh-keygen ``` on local machine

6. Open authorized_keys file

   ``` nano authorized_keys ```

7. Copy and paste contents from public key into authorized_keys

8. Grant grader ownership of the .ssh directory

   ``` chown grader:grader /home/grader/.ssh ```

9. Change permissions to .ssh directory so that only grader can read, write and
execute

   ``` chmod 700 /home/grader/.ssh ```

10. Grant grader ownership to authorized_keys

    ``` chown grader:grader /home/grader/.ssh/authorized_keys ```

11. Change permissions to authorized_keys so that only grader can read and write

    ``` chmod 600 /home/grader/.ssh/authorized_keys ```

12. SSH into server as grader

    ``` ssh -i id_rsa_ubuntu grader@34.208.16.148 ```  

    Source: [Udacity Forum](https://discussions.udacity.com/t/permission-denied-publickey-after-adding-grader-user-and-changing-ssh-port/207087/7)

## Task 5 - Update all currently installed packages

1. Run the following command

   ``` sudo apt-get update ```

2. Then run the command

   ``` sudo apt-get upgrade ```

3. Restart the machine

   ``` sudo reboot ```

## Task 6 - Change the SSH port from 22 to 2200

1. Open sshd_config

   ``` sudo nano /etc/ssh/sshd_config ```

2. Change port 22 to 2200

3. Go to Networking tab of AWS and create a new port

   ``` Custom | TCP | 2200 ```

4. Restart SSH

   ``` sudo service ssh restart ```

5. SSH into server with ```-p 2200``` included

   ``` ssh -i id_rsa_ubuntu -p 2200 grader@34.208.16.148 ```

## Task 7 - Disable root login

1. Open sshd_config

   ``` sudo nano /etc/ssh/sshd_config ```

2. Change ``` PermitRootLogin prohibit-password ``` to ``` PermitRootLogin no ```

3. Add the following at the end of the file

   ``` AllowUsers grader ```

4. Restart SSH

   ``` sudo service ssh restart ```

   Source: [AskUbuntu Forum](https://askubuntu.com/questions/16650/create-a-new-ssh-user-on-ubuntu-server)

## Task 8 - Configure the Uncomplicated Firewall (UFW)

1. Block all incoming connections on all ports:

   ``` sudo ufw default deny incoming ```

2. Allow all outgoing connections

   ``` sudo ufw default allow outgoing ```

3. Allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

   ``` sudo ufw allow 2200/tcp ```

   ``` sudo ufw allow 80/tcp ```

   ``` sudo ufw allow 123/udp ```

4. Check recent added rules

   ``` sudo ufw show added ```

5. Enable the Firewall

   ``` sudo ufw enable ```

6. Check UFW status

   ``` sudo ufw status ```

   Source: [Configuring Ports in UFW Video](https://classroom.udacity.com/nanodegrees/nd004/parts/ab002e9a-b26c-43a4-8460-dc4c4b11c379/modules/357367901175461/lessons/4331066009/concepts/48010894990923)

## Task 9 - Configure the local timezone to UTC

1. Run the command

   ``` sudo dpkg-reconfigure tzdata ```

2. Select ``` None of the above ```

3. Then choose ``` UTC ```

   Source: [AskUbuntu Forum](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt/138442)
