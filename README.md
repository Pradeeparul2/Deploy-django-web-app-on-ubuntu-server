# Deploy-django-web-app-on-ubuntu-server
deploying django app on ubuntu server with help of apache2

Assumed

1. Ubuntu server on EC2 or awslightsail
2. connect server using SSH key
3. Project files in github and ssh key added to git
4. MySQL server installed and configured.

#

1. Logged into ubuntu server using SSH key and update

		sudo apt-get update
		
2. install pip and apache2 with wsgi mod

		sudo apt-get install python3-pip apache2 libapache2-mod-wsgi-py3
		
3. create virtual environment 

		python3 -m venv myvenv
		
if getting any error try below comment

	sudo apt install -y python3-venv
		
4. activate virtual environment

		source myvenv/bin/activate

5. Now virtual environment activated then we ready to clone project from git

		git clone projectsshpathmyproject.git
		
6. navigate to project folder

		cd myproject
		
7. install requirements

		pip install -r requirements.txt
		
8. collect static assets

		python manage.py collectstatic
		
9. migrate database

		python manage.py makemigrations
		python manage.py migrate
		
10. test server is working or not

		python manage.py runserver 0.0.0.0:8000
		
it will run port on 8000 example http://yourpublicip:8000. it works fine lets configure to apache server

11. configure apache conf file for domain

		sudo nano /etc/apache2/sites-available/domainname.conf

12. add apache configuration on domainname.conf file

		WSGIDaemonProcess myproject python-path=/home/ubuntu/myproject python-home=/home/ubuntu/myvenv
		WSGIProcessGroup myproject
		WSGIScriptAlias / /home/ubuntu/myproject/myproject/wsgi.py
		<VirtualHost *:80>
			ServerAdmin webmaster@localhost
    		ServerName domainname
    		ServerAlias www.domainname
    		DocumentRoot /home/ubuntu/myproject
   			<Directory /home/ubuntu/myproject/myproject>
 			<Files wsgi.py>
 				Require all granted
 			</Files>
  			</Directory>
 			Alias /static /home/ubuntu/myproject/static
  			<Directory /home/ubuntu/myproject/static>
        		Require all granted
   			</Directory>
			Alias /media /home/ubuntu/myproject/media
  			<Directory /home/ubuntu/myproject/media>
        		Require all granted
   			</Directory>
    		ErrorLog ${APACHE_LOG_DIR}/error.log
    		CustomLog ${APACHE_LOG_DIR}/access.log combined
		</VirtualHost>

14. enable apache conf 

		sudo a2ensite domain.conf
		sudo service apache2 restart
		
13. Gave permission to server access files

		sudo chmod 664 db.sqlite3
		sudo chown :www-data db.sqlite3
		sudo chown :www-data ~/myproject
		sudo service apache2 restart
    
		
		
 
 
 


	
