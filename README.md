-> first , created a porfolio webpage where it includes accademic information about my skills, education, projects etc. i created the webpage using html, css and js.

-> secondly, hosted it online using the aws cloud services where i launched instances and create the ubuntu machine over which i cloned the repository 

-> installed apache2, java development kit and jenkins.

-> setup the login page for jenkins and created a new item and configure it through freestyle project and saved it.

-> added the webhook for the particular repository and by adding the jenkins url and thus saved it.

-> then, whatever the changes are been comitted into the repository , those changes are been reflected on the portfolio webpage directly after new jenkins job is build automatically.

-------------------------------------EC2 launch------------------------------------------------

launch the instance 
select the machine (ex:- ubuntu)
create key-pair if not existing.
allow all traffic http, ssh and https.
launch an instance.
select the instance , move to security tab , under it click on security group then edit inbound rules and add the rule type custom tcp and give port range 8080 and select 0.0.0.0/0 from cidr blocks.

-------------------------------run the machine-------------------------------------

cd downloads
ssh -i "key-name.pem" ubuntu@ip-address

-------------------------install apache2---------------------------------

# sudo -i
# apt install
# apt-get update
# apt install apache2
# cd /var/www/html -> # systemctl start apache2 -> # systemctl enable apache2

-----------------------install java---------------------------------

# apt-get update
# apt install openjdk-17-jdk openjdk-17-jre

------------------------install jenkins-----------------------------------

# sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
# echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
# apt-get update
# apt-get install jenkins -> systemctl start jenkins -> systemctl enable jenkins

--------------------allow firewall traffic-------------------------------

# ufw allow 8080
# ufw allow openSSH
# ufw enable
# ufw status
# service jenkins status

-----------------jenkins login page setup------------------------------

go to browser, enter "http://your-public-ip-address:8080" . jenkins login page will open.
to enter the password, go to terminal and type "cat /var/lib/jenkins/secrets/initialAdminPassword"
copy and paste the password.
now setup and fill the details that are been asked

--------------------creating the job---------------------------------------

create new item , select freestyle project
select git in source code management section , enter the repository url and branch also enter the credentials if your repo is private.
select "github hook trigger for GITScm polling" in build triggers section .
in build steps section , select execute shell and write :   
              rm -r /var/www/html/*
              cp -r * /var/www/html/
now save the job.

----------------------------adding webhook in repo-------------------------

go to the particular porject repo and click on setting -> select webhooks -> add webhooks.
type the payload url "http://public-ip-address:8080/github-webhook/"
select content type as application/json.
click on save.

----------------------------running the setup------------------------------

in terminal , direct to " cd /var/www/html" ->ls -> # cd /var/lib/jenkins/workspace/aws (copy this path from console ouput page of the item)
# usermod -aG www-data jenkins 
# sudo chown jenkins:www-data html

--------------------------testing the working-------------------------------

go to repo, chnage anything like in index,html page changing the name khushi's porfolio to khushi's portfolio page, commit the changes.
in jenkins, you'll observe that a job is been build automatically and then when you reload the webpage on browser you'll see the chnage that were committed by you. 
