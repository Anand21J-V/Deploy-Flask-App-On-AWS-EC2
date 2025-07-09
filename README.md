# Deploy-Flask-App-On-AWS-EC2
## Guide to Deploy Flask App on AWS EC2

### 🚀 Flask App Deployment Guide (With GitHub, Gunicorn & Nginx)

This guide walks through deploying a Flask application using:

* Python virtual environment
* Gunicorn (production WSGI server)
* Nginx (as a reverse proxy)
* Systemd (to manage the service)

---

## 🔧 1. Initial Server Setup (Ubuntu)

```bash
sudo apt-get update
sudo apt install python3 python3-pip nginx git -y
```

---

## 📁 2. Clone Your GitHub Repository

```bash
mkdir flask-app
cd flask-app

# Replace `[repo_url]` with your GitHub repo URL
git clone [repo_url]

cd [repo_name]  # Enter the cloned directory
```

---

## 🐍 3. Set Up Python Virtual Environment

```bash
sudo apt install python3-venv -y
python3 -m venv venv
source venv/bin/activate
```

---

## 📦 4. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## 🔐 5. Set Up Environment Variables

```bash
nano .env
```

Paste your API keys inside `.env`, then:

* Press `Ctrl + O` → Enter to save
* Press `Ctrl + X` to exit

---

## 🧪 6. Test Your Flask App (Optional)

```bash
python app.py
```

> Replace `app.py` with your actual entry file, if different.

---

## 🐗 7. Gunicorn Setup (Production WSGI Server)

Install Gunicorn:

```bash
pip install gunicorn
```

Run it with:

```bash
gunicorn -w 3 -b 0.0.0.0:5000 app:app
```

> ⚠️ Replace `app:app` with `filename:app_variable`
> Example: If your main file is `main.py` and Flask app is `app`, use `main:app`.

---

## 🌐 8. Nginx Reverse Proxy Configuration

### First-time Nginx Setup (Optional)

```bash
sudo mkdir -p /etc/nginx/sites-available
sudo mkdir -p /etc/nginx/sites-enabled
```

### Create Nginx Config File

```bash
sudo nano /etc/nginx/sites-available/flask-app.conf
```

Paste the following configuration:

```nginx
server {
    listen 80;
    server_name YOUR_PUBLIC_IP;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

> Replace `YOUR_PUBLIC_IP` with your actual IP address (e.g., `13.61.104.92`)

Save and exit:

* `Ctrl + O` → Enter → `Ctrl + X`

### Enable the Nginx Config

```bash
sudo ln -s /etc/nginx/sites-available/flask-app.conf /etc/nginx/sites-enabled/
```

---

## 🧪 9. Test & Restart Nginx

```bash
sudo nginx -t
```

You should see:

```
syntax is ok
test is successful
```

If successful, reload Nginx:

```bash
sudo systemctl restart nginx
```

Check status:

```bash
sudo systemctl status nginx
```

---

## ⚙️ 10. Set Up systemd Service for Auto Startup

Create systemd directory (optional):

```bash
sudo mkdir -p /etc/system/system
```

Create the service file:

```bash
sudo nano /etc/system/system/flask-app.service
```

Paste the following configuration:

```ini
[Unit]
Description=Flask app
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/flask-app/[repo_name]
ExecStart=/home/ubuntu/flask-app/[repo_name]/venv/bin/gunicorn -w 3 -b 0.0.0.0:5000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

> Replace `[repo_name]` and make sure paths are correct.
> Example: `LLMOPs-Chatbot-Deployment`

Save & exit.

---

## 🔁 11. Enable & Start the Flask Service

```bash
sudo systemctl daemon-reload
sudo systemctl restart flask-app
sudo systemctl enable flask-app
```

Also restart Nginx if needed:

```bash
sudo systemctl restart nginx
```

---

## 📊 12. Check Service Status

```bash
sudo systemctl status flask-app
sudo systemctl status nginx
```

To exit the status screen:
Press `q`

---

✅ **That's it!** Your Flask app should now be running at `http://<your_public_ip>` via Nginx and Gunicorn.

