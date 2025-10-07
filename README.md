
# 🚀 Full-Stack App Deployment on AWS EC2 with GitHub Actions


* [AWS EC2 Console](https://console.aws.amazon.com/ec2/)
* Launch an **Ubuntu 22.04** instance.
* Allow inbound rules for:

  * HTTP (80)
  * HTTPS (443)
  * Custom TCP (port 8000 or your backend port)
  * SSH (22) for access


SSH into your EC2 instance and set up the GitHub runner:

```bash
# From your EC2 terminal:
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.315.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.315.0/actions-runner-linux-x64-2.315.0.tar.gz
tar xzf actions-runner-linux-x64-2.315.0.tar.gz

# Connect your runner to GitHub (get token from repo -> Settings -> Actions -> Runners)
./config.sh --url https://github.com/your-username/MernProjectDeploy --token <TOKEN>

# Start the runner
sudo ./svc.sh install / start
```


SSH into your EC2 instance and run:

```bash
curl -sL https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.0/install.sh -o install_nvm.sh

bash install_nvm.sh

export NVM_DIR="$HOME/.nvm"
  [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
  [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

#for latest version of node use 
nvm install --lts

# for specifi verison
nvm install 22 #mention version name

sudo apt-get update
sudo apt-get install -y nginx
sudo npm i -g pm2

#If sudo does't work install directly
```


Edit the Nginx config:

```bash
sudo nano /etc/nginx/sites-available/default
```

Add inside the `server {}` block:

```nginx
location /api {
    rewrite ^/api/(.*)$ /api/$1 break;
    proxy_pass http://localhost:8000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```