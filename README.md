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

   Source: https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps

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
