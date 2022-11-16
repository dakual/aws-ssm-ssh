Install AWS Session Manager Plugin.
```sh
# https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html

curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
sudo dpkg -i session-manager-plugin.deb
```

Installing SSM Agent on Debian Server instances
```sh
wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/debian_amd64/amazon-ssm-agent.deb
sudo dpkg -i amazon-ssm-agent.deb
sudo systemctl enable amazon-ssm-agent
sudo systemctl start amazon-ssm-agent
sudo systemctl status amazon-ssm-agent
```

Installing SSM Agent on Debian Server instances with User Data
```sh
#!/bin/bash
mkdir /tmp/ssm
cd /tmp/ssm
wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/debian_amd64/amazon-ssm-agent.deb
sudo dpkg -i amazon-ssm-agent.deb
sudo systemctl enable amazon-ssm-agent
```

Creating EC2 SSM Access Role
```sh
aws iam create-role --role-name EC2SSMRole --assume-role-policy-document file://EC2AssumeRole.json
aws iam attach-role-policy --role-name EC2SSMRole --policy-arn arn:aws:iam::aws:policy/AmazonSSMFullAccess
aws iam create-instance-profile --instance-profile-name EC2SSMRole
aws iam add-role-to-instance-profile --instance-profile-name EC2SSMRole --role-name EC2SSMRole
```

Creating SSM Access Group
## AWS Systems Manager
```sh
aws iam create-group --group-name SSMAccess
aws iam put-group-policy --group-name SSMAccess --policy-name SessionPolicy --policy-document file://SessionPolicy.json
aws iam add-user-to-group --user-name dev --group-name SSMAccess
```

Start SSH Session
```sh
aws ssm start-session --target i-0101ac203e1c9c2d1
```

Port Forward from EC2 to localhost
```sh
aws ssm start-session \
    --target i-0101ac203e1c9c2d1 \
    --document-name AWS-StartPortForwardingSession \
    --parameters '{"portNumber":["80"], "localPortNumber":["8080"]}'
```