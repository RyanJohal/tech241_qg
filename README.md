# User Data Automation in Two Tier Architecture
![Alt text](<Screenshot 2023-07-21 124019.png>)


## Automating the process of downloading mongodb
#### Key points
- Downloads MongoDB
- Changes MongoDB default settings to allow app vm to access it
```
#!/bin/bash

 

# update
sudo apt update -y



# upgrade
sudo apt upgrade -y

 

# download key for the right version
wget -qO - https://www.mongodb.org/static/pgp/server-3.2.asc | sudo apt-key add -

 

# source list - specify mongo db version
echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

 

# update again
sudo apt update -y

 

# install mongo db
sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20

 

# configure bindip to 0.0.0.0
sudo sed -i 's/bindIp: 127.0.0.1/bindIp: 0.0.0.0/g' /etc/mongod.conf

 

# start mongo db
sudo systemctl restart mongod

 

# check the status
sudo systemctl status mongod

 

# enable mongo db
sudo systemctl enable mongod

```

## Automating the launch of app
#### Key points
- Installs Nginx (web server)
- Downloads app folder from github
- Creates connection between DB vm
- Uses reverse proxy to eliminate need of address extension
- Installs nodejs to run the app
- Starts the app!
```
#!/bin/bash

# update
sudo apt update -y


# upgrade
sudo apt upgrade -y


# install nginx
sudo apt install nginx -y


# status nginx
sudo systemctl status nginx


# enable nginx - will auto start on reboot
sudo systemctl enable nginx


# install sed
sudo apt install sed -y


# Change nginx config
sudo sed -i 's|try_files $uri $uri/ =404;|proxy_pass http://localhost:3000;|' /etc/nginx/sites-available/default

sudo systemctl restart nginx

# access correct node source
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -


# add in env
export DB_HOST=mongodb://172.31.44.161:27017/posts


# install node.js
sudo apt install nodejs -y


# install pm2
sudo npm install -g pm2


# copy app folder
git clone https://github.com/RyanJohal/tech241_sparta_app.git app


# cd into app folder
cd app/app


# install node js in folder
npm install -y

# seed batabase
echo "Clearing and seeding database.."
node seeds/seed.js
echo " --> Done!"
 

# kill previous app background process
pm2 kills

# Run app in the background using PM2
pm2 start app.js --name "sparta app"



```