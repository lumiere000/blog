---
#layout: article
title: aws rds cross region peering
article_header:
  type: cover
  image:
    src: /img/aws.png
#cover: /img/aws.png
tags: config network aws cloud
category: cloud
---

in this example we will have subnet like this :

|region|subnet|zone_a|zone_b|
|:---|:---|:---|:---|
|N. California|172.31.0.0/16|172.31.0.0/24|172.31.1.0/24|
|Singapore|172.32.0.0/16|172.32.0.0/24|172.32.1.0/24|

1. VPC > Create VPC (IPv4 CIDR block of each region must different)

1. VPC > Subnets > Create at least two subnets with different availability zone

1. Services > RDS > Create DB Subnet Group, pick those subnets you created above, for each region

1. Service > EC2 > Security Groups > Create Security Group, add 172.31.0.0/16 (N. California subnet) and 172.32.0.0/16 (Singapore subnet), you can also add ip where you need to access the database, like you office/home ip

1. Services > RDS > Create database, choose your setting, use your VPC, Subnet group and Security group you created

1. Services > VPC > Peering Connections > Create Peering Connection, choose your VPC requester, for example california vpc, and select another region, select your peer region and type your peer VPC ID, you can find it by : Servies > VPC > select your region at top right hand corner, go to the accepter (change region), and accept the request of VPC peering

1. After create peering connection, select peering connection again, Edit DNS Settings, Allow accepter/requester VPC to resolve DNS, you should allow both accepter and requester peering

1. Services > VPC > Route Tables > Create route table as below :

    N. California

    |Destination|Target|
    |---|---|
    |172.31.0.0/16|local|
    |0.0.0.0/0|igw-xxxxxxx|
    |172.32.0.0/16|pcx-xxxxxx|

    Singapore

    |Destination|Target|
    |---|---|
    |172.32.0.0/16|local|
    |0.0.0.0/0|igw-xxxxxxx|
    |172.31.0.0/16|pcx-xxxxxx|

1. in this case rds will create at N. California, so we go to Singapore region's EC2 instance (you can create a new instance or use your existing to test), just ping the Endpoint of the RDS which AWS provide, make sure it return 172.31.0.x

1. finally using telnet or mysql to access the Database
