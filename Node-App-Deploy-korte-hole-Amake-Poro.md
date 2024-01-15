# Step1: Make an EC2 instance

    1. Make sure to add New HTTP as a network group or allow port 22 (SSH), allow port 80 (HTTP) and allow port 443 (HTTPS) or you can make a new network gtoup
    2. Use t2.samll as you'll be needing 2GB of space to build the project
    3. select an ubuntu instance
    4. Create a pem key to connect with your instance
    5. Wait fro the instance to go live

# Step2: Connect instance with git-Bash and update the system

    1. chmod 400 <keyname.pem>
    2. ssh -i "keyname.pem" ubuntu@public IPv4 address of instance
    3. sudo apt-get update
    4. sudo apt-get upgrade

# Step3: Install git in instance

    1. sudo apt install -y git htop wget

# Step4: nvm, node and npm install

    1. wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

    # To activate the nvm need to run these commands
    2. export NVM_DIR="$HOME/.nvm"
    3. [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    4. [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

    # instalations and version check
    1. nvm install 18 -lts
    2. nvm use 18
    3. nvm --version
    4. node --version
    5. npm --version

# Step5: Head over to the working directory and clone the project

    1. cd /home/ubuntu/
    2. git clone <https://github.com/>

# Step6: Go to project directory and install dependencies

    1. ls -al
    2. cd <project directory name>
    3. ls -al
    4. npm i (Please update the npm version if it's giving you suggestion)

# Step7: Install yarn globally and convert the ts files to js files then build the project with environment setup

    1. npm i -g yarn ts-node
    2. yarn
    3. yarn build
    4. sudo nano /etc/environment (Copy and paste your project's environment variables here)

# Step8: Install pm2 and nginx

    1. npm install -g pm2
    2. sudo apt install nginx

# Step9: nginx service setup

    1. sudo nano /etc/nginx/sites-available/default
    2. Will change site name later (comment out this line as of now)
    3. setup location
        proxy_pass http://localhost:<your application's port number>;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    4. To check the nginx file syntax : sudo nginx -t
    5. sudo systemctl restart nginx.service
    6. now your EC2 instance IPv4 pblic address is available live
    7. System reboot: sudo reboot

# Step10: Run pm2 and nginx as a service and go live with http url

    1. follow Step2 (Pont 1 and 2)
    2. cd <your project folder>
    3. cd dist/
    4. pm2 start <entrypoint.js>
    5. sudo systemctl restart nginx.service
    6. pm2 logs
    7. sudo nano /etc/nginx/sites-available/default
        now open comment out and set it to your site name <test.example.com>
    8. pm2 restart <process ID>
    9. sudo systemctl restart nginx.service
    10. Your server is running with the url/site name you specified

# Step11: Configure certbot and setup HTTPS conenction

    1. sudo snap install core; sudo snap refresh core
    2. sudo apt remove certbot
    3. sudo snap install --classic certbot
    4. sudo ln -s /snap/bin/certbot /usr/bin/certbot
    5. sudo certbot --nginx -d <Your site name> Example: test.example.com
    6. sudo systemctl restart nginx.service
    7. Now your site is live with HTTPS url
    8. sudo systemctl status snap.certbot.renew.service
    9. sudo certbot renew --dry-run

# Step12: Nacha Nachi Koro

    1. Yayyyy!!! you've successfully deployed node backend application
