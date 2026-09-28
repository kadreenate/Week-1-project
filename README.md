# Week-1-project/least privilege and SoD on AWS
This lab demonstrates how to enforce least privilege and Separation of Duties (SoD) on AWS

Step 1: Create two different S3 buckets for two developers. One S3 bucket is for production, while the other is for development code. 
In the AWS Management Console > search for S3; click "Create bucket", add a globally unique name, leave all default settings as is, and click "Create bucket". That gives us this: 

<img width="1746" height="108" alt="image" src="https://github.com/user-attachments/assets/d679d791-5b8b-4c51-b451-40cc925d54ed" />


Step 2 is about creating policies that prevent engineers working on production from accessing the dev bucket and vice versa. To do this, go to IAM > Access Management > Policies > Create policy. Configure the policy for the dev bucket using this JSON file: 

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowConsoleListing",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowDevCodeAccessOnly",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::fintech-dev-code-na",
        "arn:aws:s3:::fintech-dev-code-na"
      ]
    }
  ]
}

Then fill in the name and description (optional) of the policy. This policy is for devs only. Repeat the same process for the database administrators but using this JSON file to define their permissions: 

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowConsoleListing",
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowProdDataAccessOnly",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::fintech-prod-data-na",
        "arn:aws:s3:::fintech-prod-data-na"
      ]
    }
  ]
}

These are the two policies: 

<img width="2693" height="226" alt="image" src="https://github.com/user-attachments/assets/6fbcf7a7-e91c-442f-93fb-c195f847c571" />

Step 3a: Create user groups and users for the software policies. To create a group, go to IAM dashboard > Access Management > IAM user groups > Create group. After creating the group, go to Permissions and add the policy for each group. In this instance, I attached the "fintech-software-engineer-policy" to the software-engineer group: 

<img width="2627" height="934" alt="image" src="https://github.com/user-attachments/assets/05e2e3ec-da7a-4aa1-ab39-ecd9849136c4" />


Similarly, the "fintech-dba-policy" was attached to the "database-admins" group: 

<img width="2622" height="876" alt="image" src="https://github.com/user-attachments/assets/6957fd39-4aae-412c-b477-77faf52e70de" />

Step 3b: Create users for each group. I created nathan-dev for the software-engineer group and bukunmi-dba for the database-admins group. I did this by going to the IAM dashboard > Access Management > IAM users > Create user > configure the name, password, and the group it belongs to: 

<img width="2646" height="916" alt="image" src="https://github.com/user-attachments/assets/0a373f41-8415-40f8-b0e4-94c50e429a82" />

Repeat the same process for the second user (bukunmi-dba, in this instance): 

<img width="2656" height="860" alt="image" src="https://github.com/user-attachments/assets/1a009e60-00b6-4012-9352-fb7a75365454" />

Step 4: Test the separation of Duties. Both users have list access to see the S3 buckets, but neither can perform any operations on the bucket they don't belong to. This means nathan-dev can't read/write items in fintech-prod-data-na, as shown below:  

<img width="2837" height="854" alt="image" src="https://github.com/user-attachments/assets/eeff22b1-400c-45d1-b25e-53dbe5d68502" />

Similarly, bukunmi-db can't read/write data in fintech-dev-na, as the image below shows: 

<img width="2815" height="821" alt="image" src="https://github.com/user-attachments/assets/985f37ac-1d1c-4b96-a46f-be575c943779" />

.............................................................................................................................................

<img width="1400" height="996" alt="image" src="https://github.com/user-attachments/assets/70c0ca64-6233-453a-8be1-3df4ef621307" />

