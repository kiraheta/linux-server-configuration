# Linux Server Configuration

# Overview
A secure and efficient bare-bones Ubuntu Linux web & database server that hosts a Flask web application.

## Task 1 - Setup Ubuntu Linux Server on Amazon Lightsail

Local IP address: http://52.34.99.201

Web app: http://ec2-52-34-99-201.us-west-2.compute.amazonaws.com

## Task 2 - SSH into server

1. Download private key from
https://lightsail.aws.amazon.com/ls/webapp/account/keys

2. Move private key to ```~/.ssh``` directory:

   ```mv ~/Downloads/LightsailPrivateKey.pem ~/.ssh/```

3. Change permissions so that only owner can read and write:

  ```chmod 600 ~/.ssh/LightsailPrivateKey.pem```

4. SSH into server

  ```ssh -i ~/.ssh/LightsailPrivateKey.pem ubuntu@52.34.99.201```

## Task 3 - Create user grader and grant grader permission to sudo

1. Create new user grader

  ``` sudo adduser grader ```

2. Open and edit sudoers file

     ``` sudo visudo ```

3. Add ``` grader ALL=(ALL:ALL) ALL ``` below ``` root ALL=(ALL:ALL) ALL ```

4. Save edit by entering ``` ctrl-x ```, ``` y ```, and then ``` return ```

   Source: https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps
