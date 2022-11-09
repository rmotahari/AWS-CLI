# AWS-CLI EC2 Apache Web Server
[Quelle](https://towardsaws.com/how-to-use-aws-cli-to-launch-an-ec2-web-server-with-apache-9c20d07e07be)

### Cloud9 mit Github per ssh verbindung
    
### überprufen AMI

        aws ec2 describe-images --owners amazon --filters "Name=name, Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2" "Name=state, Values=available" --query "reverse(sort_by(Images, &Name))[:1].ImageId" --output text

#### Projekt erstellen

        mkdir AWS-CLI

#### text datei erstellen

        touch resources.txt

#### Erstellen key pair

    aws ec2 create-key-pair --key-name RMMaPaChi --query 'KeyMaterial' --output text > RMMaPaChi.pem
    
#### Überprufen

    ls -lah
#### Beschreobung für Key Pair

    aws ec2 describe-key-pairs --key-name RMMaPaChi
 
#### Security Group erstellen

    aws ec2 create-security-group --group-name RMMaPaChiSG --description "Allows SSH and HTTP and HTTPS connections for the Web Server"

#### Securty Group ID:

    "GroupId": "sg-02da4a3c60b4f7d6a"
    
#### Add rules to the Security Group (SSH , HHTTP, HTTPS)

    aws ec2 authorize-security-group-ingress --group-id sg-02da4a3c60b4f7d6a --protocol tcp --port 22 --cidr 0.0.0.0/0  
    aws ec2 authorize-security-group-ingress --group-id sg-02da4a3c60b4f7d6a --protocol tcp --port 80 --cidr 0.0.0.0/0    
    aws ec2 authorize-security-group-ingress --group-id sg-02da4a3c60b4f7d6a --protocol tcp --port 443 --cidr 0.0.0.0/0    

    
#### Verify the rules created for security group

    aws ec2 describe-security-groups --group-ids sg-02da4a3c60b4f7d6a
    
    
### Create Apache script

     touch webscript.sh
     
####  Create a EC2 instance

    aws ec2 run-instances --image-id ami-0b920b0594b5288fb --count 1 --instance-type t2.micro --key-name RMMaPaChi --security-group-ids sg-02da4a3c60b4f7d6a --user-data file://webscript.sh
    
### Verify your installed EC2 instance and Apache Webserver

    aws ec2 describe-instances

### Verbindung per SSH

    chmod 400 RMMaPaChi.pem
     ssh -i RMMaPaChi.pem ec2-user@3.76.43.121

###########################################################################################################################################################################################################################################

### create a VPC with cidr 10.10.0.0/16

    aws ec2 create-vpc --cidr-block 10.10.0.0/16


### create 2 public subnets

     aws ec2 create-subnet --vpc-id vpc-0f56c3edde1c64324 --cidr-block 10.10.0.0/20
     aws ec2 create-subnet --vpc-id vpc-0f56c3edde1c64324 --cidr-block 10.10.16.0/20 --availability-zone eu-central-1b
     
### List the subnets

    aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-0f56c3edde1c64324"
    
### Subnet are assigned a public IPv4 address

    aws ec2 modify-subnet-attribute --subnet-id subnet-03a67bb375230f935 --map-public-ip-on-launch
    aws ec2 modify-subnet-attribute --subnet-id subnet-0a8307b1a277f4157 --map-public-ip-on-launch
 
 #### Create an Internet Gateway
 
    aws ec2 create-internet-gateway


#### Attach the gateway to your VPC

      aws ec2 attach-internet-gateway --vpc-id vpc-0f56c3edde1c64324 --internet-gateway-id igw-044590270c66170c1




 ##########################################################################################################################################################################################################

Uncomplicated Firewall

### Firewall Status

    sudo ufw status
    
    
### öffne die Ports

    sudo ufw allow 80
    sudo ufw allow 443
    sudo ufw allow 9443
    sudo ufw allow 81
    
### Aktiviere die Firewall    

        sudo ufw enable

###  lade sie neu

    sudo ufw reload
    
    
### Docker installieren

    sudo apt install ca-certificates curl gnupg lsb-release
    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    (lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io
    
    
### Aktiviere und starte den Docker-Dienst

    sudo systemctl start docker --now
    
    
### Docker-Gruppe hinzu

    sudo usermod -aG docker $USER
    
    
### Docker Compose installieren

    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    
### Portainer installieren

    mkdir ~/portainer
    cd ~/portainer
    
### Erstelle und öffne die Docker Compose Datei

    nano docker-compose.yaml
    
    
### fügen sie den Code

version: "3.3"
services:
    portainer:
      image: portainer/portainer-ce:latest
      container_name: portainer
      restart: always
      privileged: true
      volumes:
        - ./data:/data:Z
        - /var/run/docker.sock:/var/run/docker.sock:Z
      ports:
        - 9443:9443
    
### Starte Portainer

    docker-compose up -d
    
    
### Überprüfe den Status

    docker ps