# Whole Setup with the GitHub Repo
sudo apt-get update

sudo apt install python3 python3-pip nginx git

mkdir flask-app

cd flask-app

git clone [repo name]

cd repo name

sudo apt install python3-venv

python3 -m venv venv

source venv/bin/activate

pip3 install -r requirements.txt

nano .env [place the api keys there]
tehn ctrl+o to save and enter to continue and then ctrl + x to exit

python app.py[whatever the main folder name is]


# Whole Setup with Gunicorn [web production frame work for flask apps]

pip3 install gunicorn

gunicorn -w 3 -b 0.0.0.0:5000 app:app



# Whole Setup with Nginx[as a reverse proxy]

# Getting the permissions for the first time while setting the nginx
sudo mkdir -p /etc/nginx/sites-available
sudo mkdir -p /etc/nginx/sites-enabled

sudo nano /etc/nginx/sites-available/flask-app.conf



# If permissions already there
[Creating the file for Writing down the configurations of the Nginx]
sudo nano /etc/conf.d/flask-app.conf



# Standard Nginx Configuration files command inside of the flask-app.conf file
server{
		listen 80;
		server_name 13.61.104.92;
		
		location / {
				proxy_pass http://127.0.0.1:5000;
				proxy_set_header Host $host;
				proxy_set_header X-Real_IP $remote-addr;
				proxy_set_header X-Forwarded-For $proxy_add_forwarded_for;	
		
		}
}
 
Ctrl+O and enter and Ctrl+X


#Link nginx config into sites-enabled
sudo ln -s /etc/nginx/sites-available/flask-app.conf /etc/nginx/sites-enabled/



# For testing:
sudo nginx -t

# If everything is perfect:
then : 
syntax is ok
test is successful

# To check the status
sudo systemctl status nginx



# Whole Setup for automating the flask startup

mkdir /etc/system/system

# Creating the service file
sudo nano /etc/system/system/flask-app.service

# Standard Service Configuration files
[Unit]
Description=Flask app
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/flask-app/LLMOPs-Chatbot-Deployment
ExecStart=/home/ubuntu/flask-app/LLMOPs-Chatbot-Deployment/venv/bin/gunicorn -w 3 -b 0.0.0.0:5000 app:app
Restart=always

[Install]
WantedBy=multi-user.target

Ctrl+O and enter and Ctrl+X



# Inistialising the daemon
sudo systemctl daemon-reload

sudo systemctl restart flask-app

sudo systemctl restart nginx

# Checking the status of the services
sudo systemctl status nginx

sudo systemctl status flask-app

# starting the flaskapp service

Started flask-app.service - Flask app

press q








