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


        


    
    
    
    
    
    