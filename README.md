# Linux Server Configuration

# Overview
A secure and efficient bare-bones Ubuntu Linux web & database server that hosts a Flask web application.

## Task 1 - Setup Ubuntu Linux Server on Amazon Lightsail

Local IP address: http://34.208.16.148

Web app: http://ec2-34-208-16-148.us-west-2.compute.amazonaws.com

## Task 2 - SSH into server

1. Download private key from
https://lightsail.aws.amazon.com/ls/webapp/account/keys

2. Move private key to ``` ~/.ssh ``` directory:

   ``` mv ~/Downloads/LightsailPrivateKey.pem ~/.ssh/ ```

3. Change permissions so that only owner can read and write:

   ```chmod 600 ~/.ssh/LightsailPrivateKey.pem```

4. SSH into server

   ```ssh -i ~/.ssh/LightsailPrivateKey.pem ubuntu@34.208.16.148```

   Source: [Stackoverflow](https://stackoverflow.com/questions/14229846/connecting-to-amazon-aws-linux-server-by-ssh-on-mac)

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

## Task 10 - Install and configure Apache to serve a Python mod_wsgi application

1. Install the Apache Web Server

   ``` sudo apt-get install apache2 ```

2. Go to http://34.208.16.148

   If successful, you will see the Apache2 Ubuntu Default Page

3. Install mod_wsgi

   ``` sudo apt-get install libapache2-mod-wsgi ```

4. Configure Apache to handle requests via WSGI

   ``` sudo nano /etc/apache2/sites-enabled/000-default.conf ```

5. Add the following line before the </ VirtualHost> closing tag

   ``` WSGIScriptAlias / /var/www/html/myapp.wsgi ```

6. Restart Apache server

   ``` sudo apache2ctl restart ```

7. Test Apache configuration via a simple application

   ``` sudo nano /var/www/html/myapp.wsgi ```

8. Add the following to the file

   ```
   def application(environ, start_response):
    status = '200 ok'
    output = 'Hello World. Configuration successful!'

    response_headers=[('content-type','text/plain'),('content-length', str(len(output)))]
    start_response(status, response_headers)
    return [output]
   ```
9. Reload http://34.208.16.148

   If successful, you will see the line, 'Hello
   World. Configuration successful!', instead of the Apache2 Ubuntu Default Page

   Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-the-apache-web-server-on-an-ubuntu-or-debian-vps)

## Task 11 - Install and configure Git

1. Install Git

   ``` sudo apt-get install git ```

2. Configure name and email address

   ``` git config --global user.name "Your Name" ```

   ``` git config --global user.email "youremail@domain.com" ```

   Source: [How To Install Git on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04)

## Task 12 - Setup environment to deploy Flask application

1. Install python-dev package

   ``` sudo apt-get install python-dev```

2. Check if module wsgi is enabled

   ``` sudo a2enmod wsgi ```

3. Go to www directory

   ``` cd /var/www ```

4. Create new directory ``` catalog ```

   ``` sudo mkdir catalog ```

5. ``` cd ``` into ``` catalog ```

6. Grant grader ownership to catalog directory

   ``` sudo chown -R grader:grader catalog ```

7. Create new directory catalog

   ``` sudo mkdir catalog ```

8. ``` cd ``` into catalog

9. Create ``` static ``` and ``` template ``` directories

   ``` sudo mkdir static templates ```

10. Create __init__.py file that will contain the flask application logic

    ``` sudo nano __init__.py ```

11. Add the following logic into it

     ```
     from flask import Flask
      app = Flask(__name__)
      @app.route("/")
      def hello():
          return "Hello Universe!"
      if __name__ == "__main__":
          app.run()
     ```

## Task 13 - Install Flask

1. Install pip installer

   ``` sudo apt-get install python-pip ```

2. Install virtualenv

   ``` sudo pip install virtualenv  ```

3. Set virtual environment name to 'venv'

   ``` sudo virtualenv venv ```

4. Enable all permissions to all users

   ``` sudo chmod -R 777 venv ```

5. Activate virtual environment

   ``` source venv/bin/activate ```

6. Install Flask from within virtual environment

   ``` pip install Flask ```

7. Run the app to test if the installation is successful

   ``` python __init__.py ```

   It should display “Running on http://localhost:5000/” or "Running on http://127.0.0.1:5000/". If you see this message, you have successfully configured the app

8. Deactivate the environment

   ``` deactivate ```

   Source: [DigitalOcean ](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

## Task 14 - Configure and Enable a New Virtual Host

1. Create a Virtual Host file

   ``` sudo nano /etc/apache2/sites-available/catalog.conf ```

2. Add the following lines of code

       <VirtualHost *:80>
                       ServerName 34.208.16.148
                       ServerAdmin admin@34.208.16.148
                       WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                       <Directory /var/www/catalog/catalog/>
                               Order allow,deny
                               Allow from all
                       </Directory>
                       Alias /static /var/www/catalog/catalog/static
                       <Directory /var/www/catalog/catalog/static/>
                               Order allow,deny
                               Allow from all
                       </Directory>
                       ErrorLog ${APACHE_LOG_DIR}/error.log
                       LogLevel warn
                       CustomLog ${APACHE_LOG_DIR}/access.log combined
       </VirtualHost>

3. Enable the virtual host

   ``` sudo a2ensite catalog ```

4. Create the wsgi file

   ``` cd /var/www/catalog ```

   ``` sudo nano catalog.wsgi ```

5. Add the following lines of code to the wsgi file

        #!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/var/www/catalog/")

        from catalog import app as application
        application.secret_key = 'Add your secret key'

6. Restart Apache

   ``` sudo service apache2 restart ```

   Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)

## Task 15 - Clone github repository

1. Clone Project 3 Repo into ``` /var/www/catalog ```

   ```  git clone https://github.com/kiraheta/item-catalog-app.git```

2. ``` cd ``` into ``` item-catalog-app/catalog ```

3. ``` mv ``` files from ``` catalog ``` to ``` catalog ```

   ``` sudo mv catalog /var/www/catalog/catalog/ ```

4. Delete directory

   ``` sudo rm -rf item-catalog-app/```

## Task 16 - Make .git directory not publicly accessible via a browser

1. ``` cd ``` into ``` /var/www/catalog/ ```

2. Create and open .htaccess file

   ``` sudo nano .htaccess ```

3. Add the following into file

   ``` RedirectMatch 404 /\.git ```

   Source: [Stackoverflow](https://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible)

## Task 17 - Install additional dependencies

1. Active virtualenv & then install packages

   ``` source venv/bin/activate ```

   ``` pip install httplib2  ```

   ``` pip install requests  ```

   ``` pip install --upgrade oauth2client  ```

   ``` sudo pip install sqlalchemy  ```

   ``` pip install flask-sqlalchemy ```

   ``` sudo apt-get install python-psycopg2  ```  

   ``` pip install psycopg2 ```  

## Task 18 - Install and configure PostgreSQL

1. Install PostgreSQL

   ``` sudo apt-get install postgresql ```

   ``` sudo apt-get install postgresql-contrib ```

2. Check that remote connections are not allowed

   ``` sudo nano /etc/postgresql/9.5/main/pg_hba.conf ```

3. Open  ``` database_setup.py ``` file

   ``` sudo nano database_setup.py ```

4. Update engine from

   ``` engine = create_engine('sqlite:///categoryitems.db') ```

   to

   ``` engine = create_engine('postgresql://catalog:dbpassword@localhost/catalog') ```

5. Update engine in ``` application.py ``` and ``` lotofitems.py ```

6. Rename ``` application.py ``` to ``` __init__.py ```

   ``` mv application.py __init__.py ```

7. Switch to user postgres

   ``` sudo su - postgres ```

8. Log into PostgreSQL

   ``` psql ```

9. Create user catalog

   ``` CREATE USER catalog WITH PASSWORD 'dbpassword'; ```

10. Grant user catalog the ability to create database tables

    ``` ALTER USER catalog CREATEDB; ```

11. Check current roles and their attributes

    ``` \du ```

12. Create database

    ``` CREATE DATABASE catalog WITH OWNER catalog; ```

13. Connect to database

    ``` \c catalog ```

14. Revoke all rights

    ``` REVOKE ALL ON SCHEMA public FROM public; ```

15. Grant access solely to user catalog

    ``` GRANT ALL ON SCHEMA public TO catalog; ```

16. Disconnect from database

    ``` \q ```

17. Log out of PostgreSQL

    ``` exit ```

18. Create Postgresql database schema

    ``` python database_setup.py ```

19. Populate tables

    ``` python lotsofitems.py ```

20. Restart Apache

    ``` sudo service apache2 restart ```

21. Go to public IP address: http://34.208.16.148

    Run ``` sudo tail -20 /var/log/apache2/error.log ``` to access the Apache
    error log file if you get an internal server error

    Source: [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)

## Task 19 - Update catalog.wsgi file if you get internal server error

1. Open catalog.wsgi file

   ``` sudo nano /var/www/catalog/catalog.wsgi ```

2. Add the following lines to the top of your .wsgi file

   ```python     
   activate_this = '/path/to/env/bin/activate_this.py'
   execfile(activate_this, dict(__file__=activate_this)) ```

   Source: [Working with Virtual Environments](http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/#working-with-virtual-environments)

## Task 20 - Update path to client_secrets.json file

1. Open __init__.py file

   ``` sudo nano /var/www/catalog/catalog/__init__.py ```

2. Change

   ``` CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web']['client_id'] ```

   to

   ``` CLIENT_ID = json.loads(open('/var/www/catalog/catalog/client_secrets.json', 'r').read())['web']['client_id'] ```

3. Restart Apache

    ``` sudo service apache2 restart ```

   Source: [Udacity Forum](https://discussions.udacity.com/t/no-such-file-or-direcoty/236279)

## Task 21 - Update Google OAuth

1. Open catalog.conf file

   ``` sudo nano /etc/apache2/sites-available/catalog.conf ```

2. Add the hostname below ServerName

   ``` ServerAlias ec2-34-208-16-148.us-west-2.compute.amazonaws.com ```

3. Enable the virtual host

   ``` sudo a2ensite catalog ```

4. Restart Apache

   ``` sudo service apache2 restart ```

5. Go to the project on the Developer Console

   https://console.developers.google.com/

6. Go to Credentials/Edit Settings

7. Update Authorized JavaScript origins

   ``` http://ec2-34-208-16-148.us-west-2.compute.amazonaws.com ```

   ``` http://34.208.16.148 ```

8. Update Authorized redirect URIs

  ``` http://ec2-34-208-16-148.us-west-2.compute.amazonaws.com/oauth2callback ```

9. Save and download client_secrets.json

10. Replace old client_secrets.json with new one

11. Downgrade packages

  ``` pip install werkzeug==0.8.3 ```

  ``` pip install flask==0.9 ```

  ``` pip install Flask-Login==0.1.3 ```

  Source: [Ubuntu Forum](https://discussions.udacity.com/t/oauth-course-google-sign-in-doesnt-work/15444)
