+++
title = "Khởi tạo S3 Bucket"
date = 2020-05-14T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

#### S3
**Amazon Simple Storage Service** (Amazon S3) là dịch vụ lưu trữ đối tượng được xây dựng để lưu trữ và truy xuất bất kỳ lượng dữ liệu nào từ mọi nơi trên web.

#### Tác dụng của S3 trong workshop

Trong workshop này, S3 sẽ được sử dụng để:
- **Lưu trữ static content**: Hình ảnh, CSS, JavaScript của website

#### Tạo S3 Bucket
Trong phần này, chúng ta sẽ tạo một S3 bucket để lưu trữ các tài nguyên tĩnh cho website. Hãy làm theo các bước sau:

{{% notice info %}}
S3 bucket name phải là duy nhất trên toàn cầu. Nếu tên bạn chọn đã được sử dụng, hãy thử một tên khác.
{{% /notice %}}

#### 1. Truy cập S3 Console
- Mở trình duyệt và truy cập vào Amazon S3 console tại https://console.aws.amazon.com/s3/

#### 2. Tạo Bucket mới
- Chọn **Create bucket**
- Nhập tên bucket độc nhất của bạn

#### 3. Cấu hình bucket
- Bỏ chọn "Block all public access" (vì chúng ta cần public access cho web assets)
- Xác nhận bạn hiểu rằng bucket sẽ public
- Các tùy chọn khác giữ nguyên mặc định

![create-s3](/images/5-Create-Amazon-S3/1.png)
#### 4. Tạo bucket policy
- Sau khi tạo bucket, chọn bucket của bạn
![s3](/images/5-Create-Amazon-S3/2.png)
- Vào tab **Permissions** trên tài khoản **root**
- Chọn **Bucket Policy**
![s3](/images/5-Create-Amazon-S3/3.png)
- Thêm policy cho phép public access:

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
- Vào tab **Objects**
- Chọn **Upload**
- Kéo thả files hoặc chọn **Add files**
- Chọn **Upload**

#### 6. Kiểm tra truy cập
- Chọn file vừa upload
- Copy Object URL và mở trong trình duyệt
- Nếu thấy nội dung file, bạn đã cấu hình thành công

{{% notice warning %}}
Đảm bảo chỉ cho phép public access với những files cần thiết cho website. Không nên public toàn bộ bucket nếu có dữ liệu nhạy cảm.
{{% /notice %}}

### Kết nối S3 với Node.js

1. Cài đặt AWS SDK cho Node.js:
```bash
npm install aws-sdk
```

2. Ví dụ code kết nối và sử dụng S3:
```javascript
const AWS = require('aws-sdk');

// Cấu hình AWS
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
**Best Practices khi sử dụng S3:**
- Luôn sử dụng environment variables cho credentials
- Implement retry logic cho upload/download
- Sử dụng presigned URLs cho temporary access
- Implement proper error handling
- Nên dùng AWS SDK v3 cho Node.js projects mới
{{% /notice %}}

#### Tài liệu tham khảo
1. [AWS SDK for JavaScript Documentation](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html)
2. [S3 Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
3. [Using presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-urls.html)
4. [AWS SDK v3 for JavaScript](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/getting-started.html)

### Hoàn Thành !