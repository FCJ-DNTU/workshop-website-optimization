+++
title = "Triển khai Website Lên EC2"
date = 2020-05-14T00:38:32+07:00
weight = 6
chapter = false
pre = "<b>6. </b>"
+++


Trong phần này, chúng ta sẽ học cách triển khai một ứng dụng Node.js lên EC2 instance. Quy trình này bao gồm việc cài đặt các dependencies cần thiết, cấu hình môi trường, và thiết lập service để đảm bảo ứng dụng chạy ổn định và liên tục.

Các bước chính bao gồm:
- Cài đặt Node.js
- Clone và cài đặt ứng dụng từ repository
- Cấu hình environment variables
- Thiết lập systemd service
- Kiểm tra và xác nhận deployment


### Triển khai Node.js Application

#### 1. Cài đặt Node.js
```bash
# Cài đặt Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verify cài đặt
node --version
npm --version

```

#### 2. Clone và Cài đặt Ứng dụng
```bash
# Tạo thư mục ứng dụng
mkdir -p /home/ubuntu/nodejs-app
cd /home/ubuntu/nodejs-app

# Verify thư mục đã được tạo
ls -la

# Clone repository
git clone <YOUR_REPOSITORY_URL>

# Verify repository đã được clone
ls -la

# Di chuyển vào thư mục project
cd <YOUR_PROJECT_FOLDER>

# Kiểm tra cấu trúc project
ls -la

# Cài đặt dependencies
npm install

# Verify node_modules
ls -la node_modules
```
![setup-github](/images/6-Deploy-Project-To-EC2/1.png)

{{% notice info %}}
**Thay thế các giá trị:**
- `<YOUR_REPOSITORY_URL>`: URL của Git repository
- `<YOUR_PROJECT_FOLDER>`: Tên thư mục sau khi clone
{{% /notice %}}

#### 3. Cấu hình Environment Variables (Nếu có)
```bash
# Di chuyển vào thư mục project
cd /home/ubuntu/nodejs-app/<YOUR_PROJECT_FOLDER>

# Verify đường dẫn hiện tại
pwd

# Tạo file .env
cat > .env << 'EOL'
PORT=<YOUR_PORT>
MONGO_URL=<YOUR_MONGODB_URL>
NODE_ENV=<YOUR_NODE_ENV>
CLOUD_NAME=<YOUR_CLOUDINARY_NAME>
API_KEY_CLOUDINARY=<YOUR_CLOUDINARY_API_KEY>
API_KEY_SECRET_CLOUDINARY=<YOUR_CLOUDINARY_API_SECRET>
EOL

# Verify file .env đã được tạo
ls -la .env
cat .env
```

#### 4. Cấu hình Systemd Service
```bash
# Tạo service file
sudo nano /etc/systemd/system/nodejs-app.service
```
Sau khi điền vào, trong giao diện sẽ trông như sau
![systemd](/images/6-Deploy-Project-To-EC2/2.png)

Sau đó điền vào nội dung sau

Nội dung file `nodejs-app.service`:
```ini
[Unit]
Description=Node.js App
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/nodejs-app/<YOUR_PROJECT_FOLDER>
ExecStart=/usr/bin/npm start
Restart=always
Environment=NODE_ENV=<YOUR_NODE_ENV>
Environment=PORT=<YOUR_PORT>

[Install]
WantedBy=multi-user.target
```

![systemd](/images/6-Deploy-Project-To-EC2/3.png)

Sau khi dán xong ta dùng lần lượt **Ctrl+X**, **Y** và **Enter** để thoát ra.


#### 5. Khởi động Service để chạy Website
```bash
# Verify service file
ls -la /etc/systemd/system/nodejs-app.service
cat /etc/systemd/system/nodejs-app.service

# Reload systemd
sudo systemctl daemon-reload

# Enable service
sudo systemctl enable nodejs-app

# Start service
sudo systemctl start nodejs-app

# Check status
sudo systemctl status nodejs-app

# Verify process
ps aux | grep node
ls -la /proc/$(pgrep -f "npm start")/cwd
```
![deploy](/images/6-Deploy-Project-To-EC2/4.png)
### Test và Verify Deployment

#### 1. Kiểm tra Service Status
```bash
# Verify working directory
pwd
ls -la

# Check service logs
sudo journalctl -u nodejs-app -f

# Verify process
ps aux | grep node

# Check port listening
sudo netstat -tulpn | grep <YOUR_PORT>

# Check file permissions
ls -la /home/ubuntu/nodejs-app/<YOUR_PROJECT_FOLDER>
```

#### 2. Test Endpoints
```bash
# Test local connection
curl http://localhost:<YOUR_PORT>/

# Test từ public IP
curl http://<EC2_PUBLIC_IP>:<YOUR_PORT>/
hoặc
http://<EC2_PUBLIC_IP>:<YOUR_PORT>/
```
![deploy](/images/6-Deploy-Project-To-EC2/6.png)


{{% notice tip %}}
**Thay thế các placeholder:**
- ![deploy](/images/6-Deploy-Project-To-EC2/5.png)
- `<EC2_PUBLIC_IP>`: IPv4 Public của EC2 instance (ấn vào EC2 để lấy IPv4)
- `<YOUR_PORT>`: Port đã cấu hình trong .env
{{% /notice %}}

#### Như vậy chúng ta đã truy cập được vào Website, hãy giữ lại EC2 instance này để lát chúng ta cùng kiểm tra hiệu năng nhé!


Trong phần tiếp theo, chúng ta sẽ tìm hiểu về:
- Application Load Balancer (ALB)
- Auto Scaling Group
- Monitoring
### Hoàn Thành !