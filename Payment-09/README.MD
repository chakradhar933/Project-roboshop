### 10-Payment
- This service is responsible for payments in RoboShop e-commerce app. This service is written on Python 3.6, So need it to run this app.

* HINT
- Developer has chosen Python, Check with developer which version of Python is needed.

- Install Python 3.6
```bash
yum install python36 gcc python3-devel -y
```
- Configure the application.

* INFO
- Our application developed by the developer of our org and it is not having any RPM software just like other softwares. So we need to configure every step manually

* RECAP
- We already discussed in Linux basics section that applications should run as nonroot user.

- Add application User
```bash
useradd roboshop
```
* INFO
- User roboshop is a function / daemon user to run the application. Apart from that we dont use this user to login to server.

- Also, username roboshop has been picked because it more suits to our project name.

* INFO
- We keep application in one standard location. This is a usual practice that runs in the organization.

- Lets setup an app directory.
```bash
mkdir /app 
```
- Download the application code to created app directory.
```bash
curl -L -o /tmp/payment.zip https://roboshop-artifacts.s3.amazonaws.com/payment.zip 
cd /app 
unzip /tmp/payment.zip
```
- Every application is developed by development team will have some common softwares that they use as libraries. This application also have the same way of defined dependencies in the application configuration.

- Lets download the dependencies.
```bash
cd /app 
pip3.6 install -r requirements.txt
```
- We need to setup a new service in systemd so systemctl can manage this service

* INFO
- We already discussed in linux basics that advantages of systemctl managing services, Hence we are taking that approach. Which is also a standard way in the OS.

- Setup SystemD Payment Service

- /etc/systemd/system/payment.service
```bash
[Unit]
Description=Payment Service

[Service]
User=root
WorkingDirectory=/app
Environment=CART_HOST=<CART-SERVER-IPADDRESS>
Environment=CART_PORT=8080
Environment=USER_HOST=<USER-SERVER-IPADDRESS>
Environment=USER_PORT=8080
Environment=AMQP_HOST=<RABBITMQ-SERVER-IPADDRESS>
Environment=AMQP_USER=roboshop
Environment=AMQP_PASS=roboshop123

ExecStart=/usr/local/bin/uwsgi --ini payment.ini
ExecStop=/bin/kill -9 $MAINPID
SyslogIdentifier=payment

[Install]
WantedBy=multi-user.target
```
* INFO
* Hint! You can create file by using vim /etc/systemd/system/payment.service

# Load the service.
```bash
systemctl daemon-reload
```
* INFO
- This above command is because we added a new service, We are telling systemd to reload so it will detect new service.

- Start the service.
```bash
systemctl enable payment 
systemctl start payment
```
