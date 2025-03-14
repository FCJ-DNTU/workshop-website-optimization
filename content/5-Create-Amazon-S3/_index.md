+++
title = "Create S3 Bucket"
date = 2020-05-14T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

#### S3
**Amazon Simple Storage Service** (Amazon S3) is an object storage service built to store and retrieve any amount of data from anywhere on the web.

#### Purpose of S3 in workshop

In this workshop, S3 will be used to:
- **Store static content**: Website images, CSS, JavaScript

#### Create S3 Bucket
In this section, we will create an S3 bucket to store static resources for the website. Follow these steps:

{{% notice info %}}
S3 bucket names must be globally unique. If your chosen name is already taken, try another name.
{{% /notice %}}

#### 1. Access S3 Console
- Open your browser and access the Amazon S3 console at https://console.aws.amazon.com/s3/

#### 2. Create New Bucket
- Select **Create bucket**
- Enter your unique bucket name

#### 3. Configure bucket
- Uncheck "Block all public access" (because we need public access for web assets)
- Confirm that you understand the bucket will be public
- Keep other options as default

![create-s3](/images/5-Create-Amazon-S3/1.png)
#### 4. Create bucket policy
- After creating the bucket, select your bucket
![s3](/images/5-Create-Amazon-S3/2.png)
- Go to **Permissions** tab on the **root** account
- Select **Bucket Policy**
![s3](/images/5-Create-Amazon-S3/3.png)
- Add policy to allow public access:

```json
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "AllowPublicRead",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::optimization-website/*"
        }
    ]
}
```
![s3](/images/5-Create-Amazon-S3/4.png)

#### 5. Upload files
- Go to **Objects** tab
- Select **Upload**
- Drag and drop files or select **Add files**
- Select **Upload**

#### 6. Verify access
- Select the uploaded file
- Copy Object URL and open in browser
- If you can see the file content, you've configured successfully

{{% notice warning %}}
Ensure to only allow public access for files necessary for the website. Don't make the entire bucket public if it contains sensitive data.
{{% /notice %}}

### Connect S3 with Node.js

1. Install AWS SDK for Node.js:
```bash
npm install aws-sdk
```

2. Example code for connecting and using S3:
```javascript
const AWS = require('aws-sdk');

// Configure AWS
AWS.config.update({
    accessKeyId: 'YOUR_ACCESS_KEY',
    secretAccessKey: 'YOUR_SECRET_KEY',
    region: 'ap-southeast-1'
});

const s3 = new AWS.S3();

// Upload file
async function uploadFile(file, bucket, key) {
    const params = {
        Bucket: bucket,
        Key: key,
        Body: file
    };
    
    try {
        const data = await s3.upload(params).promise();
        console.log('File uploaded successfully:', data.Location);
        return data.Location;
    } catch (err) {
        console.error('Error uploading file:', err);
        throw err;
    }
}

// Download file
async function getFile(bucket, key) {
    const params = {
        Bucket: bucket,
        Key: key
    };

    try {
        const data = await s3.getObject(params).promise();
        return data.Body;
    } catch (err) {
        console.error('Error getting file:', err);
        throw err;
    }
}
```

{{% notice tip %}}
**Best Practices when using S3:**
- Always use environment variables for credentials
- Implement retry logic for upload/download
- Use presigned URLs for temporary access
- Implement proper error handling
- Should use AWS SDK v3 for new Node.js projects
{{% /notice %}}

#### References
1. [AWS SDK for JavaScript Documentation](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html)
2. [S3 Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
3. [Using presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-urls.html)
4. [AWS SDK v3 for JavaScript](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/getting-started.html)

### Complete!