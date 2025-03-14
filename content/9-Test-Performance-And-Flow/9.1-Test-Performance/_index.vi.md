+++
title = "Kiểm tra hiệu năng"
weight = 1
pre = "<b>9.1 </b>"
+++

### Test Hiệu Năng Ứng Dụng 

#### 1. Chuẩn bị Test (Trên máy local)

1. **Cài đặt k6**
```bash
# Windows (sử dụng chocolatey)
choco install k6

# MacOS
brew install k6

# Linux
sudo gpg --no-default-keyring --keyring /usr/share/keyrings/k6-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C5AD17C747E3415A3642D57D77C6C491D6AC1D69
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt-get update
sudo apt-get install k6
```

2. **Verify cài đặt**
```bash
k6 version
```

#### 2. Tạo Script Test

1. **Tạo file `test-performance.js`**
```javascript
import http from "k6/http";
import { check, sleep } from "k6";

export const options = {
  stages: [
    { duration: "3m", target: 100 }, // Tăng dần lên 100 users trong 3 phút
    { duration: "5m", target: 100 }, // Duy trì 100 users trong 5 phút
    { duration: "2m", target: 0 },   // Giảm về 0 trong 2 phút
  ],
  rps: 200,                         // 200 requests/second
  thresholds: {
    http_req_duration: ["p(95)<500"], // 95% requests dưới 500ms
    http_req_failed: ["rate<0.01"],   // Tỷ lệ lỗi dưới 1%
  },
};

export default function () {
  const BASE_URL = "http://<YOUR_ALB_DNS>/"; // Thay thế bằng IP:PORT Hoặc ALB DNS của bạn

  // Test homepage
  const response = http.get(BASE_URL);

  // Verify response
  check(response, {
    "status is 200": (r) => r.status === 200,
  });

  sleep(1);
}
```

#### 3. Cách Chạy Test
```bash
# Lưu script vào file test-performance.js
k6 run test-performance.js
```

#### 4 Kịch Bản Test Tăng Dần
1. Test Cơ Bản (Warm Up)
```javascript
import http from "k6/http";
import { check } from "k6";

export const options = {
  stages: [
    { duration: '1m', target: 100 },    // Tăng đều
    { duration: '2m', target: 100 },    // Giữ ổn định
    { duration: '1m', target: 0 }       // Kết thúc
  ],
  rps: 200
};

export default function () {
  http.get("http://<YOUR_ALB_DNS>/");
}
```
*Mục đích: Khởi động hệ thống, test cơ bản*


2. Test Nặng (High Load)
```javascript
import http from "k6/http";
import { check, sleep } from "k6";

export const options = {
  stages: [
    { duration: '3m', target: 500 },    // Tăng mạnh
    { duration: '5m', target: 500 },    // Giữ tải cao
    { duration: '2m', target: 0 }       // Kết thúc
  ],
  rps: 1000
};

export default function () {
  // Heavy computation
  let result = 0;
  for(let i = 0; i < 100000; i++) {
    result += Math.sqrt(i);
  }

  // Concurrent requests
const response = http.get(BASE_URL + "/");

  check(responses, {
    "status is 200": (r) => r.status === 200
  });

  sleep(0.1);
}
```

{{% notice tip %}}
**Cách Chạy Test:**
1. Chạy lần lượt từng script
2. Đợi hệ thống ổn định giữa các lần test (5-10 phút)
3. Monitor và ghi lại kết quả mỗi level
{{% /notice %}}

### Trên EC2 instance
#### Kết quả sau khi kiểm tra hiệu năng
**Test Cơ Bản (Warm Up)**
![target-cpu](/images/9-Testperformance-And-Flow/1.png)
**Test Nặng (High Load)**
![target-cpu](/images/9-Testperformance-And-Flow/3.png)
#### Phân Tích Chi Tiết Performance Tests
 1. Thông Số Cơ Bản
| Metric | Test 1 (100 VUs) | Đánh Giá | Test 2 (500 VUs) | Đánh Giá |
|--------|------------------|-----------|------------------|-----------|
| Thời lượng | 4 phút | ✓ | 15 phút | ✓ |
| Tổng requests | 3,422 | ✓ | 26,354 | ✓ |
| Requests/giây | 14.25 | ⚠️ Thấp | 29.28 | ❌ Rất thấp với 500 VUs |
| Success rate | 100% | ✓ | 100% | ✓ |

2. Response Time
| Metric | Test 1 (100 VUs) | Đánh Giá | Test 2 (500 VUs) | Đánh Giá |
|--------|------------------|-----------|------------------|-----------|
| Trung bình | 5.24s | ❌ Cao | 12.95s | ❌ Cực kỳ cao |
| Tối thiểu | 343.39ms | ✓ | 212.27ms | ✓ |
| Trung vị | 4.84s | ❌ Cao | 15.83s | ❌ Không chấp nhận |
| Tối đa | 55.84s | ❌ Spike lớn | 18.16s | ❌ Quá cao |
| p90 | 7.75s | ❌ Kém | 16.84s | ❌ Rất kém |
| p95 | 10.26s | ❌ Kém | 16.88s | ❌ Rất kém |

3. Request Phases
| Phase | Test 1 (100 VUs) | Đánh Giá | Test 2 (500 VUs) | Đánh Giá |
|-------|------------------|-----------|------------------|-----------|
| Blocking | 134.05ms | ⚠️ Cao | 1.37ms | ✓ |
| Connecting | 58.63ms | ✓ | 1.35ms | ✓ |
| Waiting | 3.99s | ❌ Cao | 12.95s | ❌ Cực kỳ cao |
| Receiving | 1.1s | ⚠️ Cao | 307.01µs | ✓ |
| Sending | 143.45ms | ✓ | 968.27µs | ✓ |

4. Data Transfer
| Metric | Test 1 (100 VUs) | Đánh Giá | Test 2 (500 VUs) | Đánh Giá |
|--------|------------------|-----------|------------------|-----------|
| Data received | 924 kB/s | ✓ | 76 kB/s | ⚠️ Thấp |
| Data sent | 1.2 kB/s | ✓ | 2.5 kB/s | ✓ |

#### Phân Tích Chính

1. **Vấn Đề Nghiêm Trọng:**
```bash
- Response time quá cao ở cả 2 test
- Waiting time là bottleneck chính
- Throughput không tăng tương ứng với số VUs
- p90/p95 cho thấy hiệu năng kém ổn định
```

### Trên EC2 instance sau khi sử dụng ALB, AutoScaling
#### Kết quả sau khi kiểm tra hiệu năng
**Test Cơ Bản (Warm Up)**
![target-cpu](/images/9-Testperformance-And-Flow/7.png)

1. **Bảng So Sánh Tổng Quan**
| Metric | Trước (EC2) | Sau (ALB+ASG) | % Cải Thiện | Đánh Giá |
|--------|-------------|---------------|-------------|-----------|
| Throughput (req/s) | 14.25 | 16.92 | +18.7% | ✓ Tăng đáng kể |
| Avg Response Time | 5.24s | 4.46s | -14.9% | ✓ Giảm tốt |
| Max Response Time | 55.84s | 10.16s | -81.8% | ✓ Cải thiện rõ rệt |
| Blocking Time | 134.05ms | 1.04ms | -99.2% | ✓ Giảm mạnh |
| Data Transfer | 924 kB/s | 1.1 MB/s | +19% | ✓ Tăng hiệu quả |

2. **Cải Thiện Chính**
| Khía Cạnh | Trước | Sau | Kết Quả |
|-----------|-------|-----|----------|
| Xử lý tải | Giảm hiệu năng khi tải cao | Ổn định ở tải cao | ✓ Đạt |
| Độ ổn định | Nhiều spike (55.84s) | Ít spike (max 10.16s) | ✓ Đạt |
| Kết nối | Thời gian chờ cao | Giảm 99% thời gian chờ | ✓ Đạt |
| Băng thông | Giới hạn bởi 1 instance | Phân phối qua nhiều instance | ✓ Đạt |

3. **Tóm Tắt Cải Thiện**
```bash
Đã Giải Quyết:
- Bottleneck xử lý tải ✓
- Spike response time ✓
- Thời gian chờ kết nối ✓
- Khả năng mở rộng ✓

```

4. **Kết Luận**
```bash
- ALB+ASG đã cải thiện hiệu năng tổng thể 18.7%
- Giảm 81.8% thời gian phản hồi tối đa
- Hệ thống ổn định và đáng tin cậy hơn
- Sẵn sàng cho việc mở rộng quy mô
- Tuy nhiên lần kiểm tra này mới chỉ nằm ở mức cơ bản, mới chỉ áp dụng được alb và chưa đến mức sử dụng Auto Scaling. Trong phần tiếp theo, chúng ta hãy cùng nhau kiểm tra hiệu năng ở mức nặng để biết được kết quả sau khi sử dụng Auto Scaling.
```

**Test Nặng (High Load)**
![target-cpu](/images/9-Testperformance-And-Flow/10.png)
#### So Sánh Kết Quả Hai Lần Test Với 500 VUs

1. **Bảng So Sánh Tổng Quan**
| Metric | Test 1 | Test 2 | % Thay Đổi | Đánh Giá |
|--------|--------|--------|------------|-----------|
| Tổng Requests | 26,354 | 37,080 | +40.7% | ✓ Tăng đáng kể |
| Throughput (req/s) | 29.28 | 41.18 | +40.6% | ✓ Cải thiện rõ rệt |
| Success Rate | 100% | 99.84% | -0.16% | ⚠️ Giảm nhẹ |
| Avg Response Time | 12.95s | 9.15s | -29.3% | ✓ Giảm tốt |

2. **Chi Tiết Response Time**
| Metric | Test 1 | Test 2 | Đánh Giá |
|--------|--------|--------|-----------|
| Min | 212.27ms | 27.53ms | ✓ Cải thiện |
| Median | 15.83s | 4.87s | ✓ Giảm mạnh |
| Max | 18.16s | 50.83s | ❌ Tăng cao |
| p90 | 16.84s | 16.96s | ≈ Tương đương |
| p95 | 16.88s | 29.72s | ❌ Tăng cao |

3. **Phân Tích Phases**
| Phase | Test 1 | Test 2 | Đánh Giá |
|-------|--------|--------|-----------|
| Blocking | 1.37ms | 658.73µs | ✓ Giảm 51.9% |
| Connecting | 1.35ms | 607.27µs | ✓ Giảm 55% |
| Waiting | 12.95s | 9.14s | ✓ Giảm 29.4% |
| Receiving | 307.01µs | 316.91µs | ≈ Tương đương |
| Sending | 968.27µs | 1.04ms | ≈ Tương đương |

4. **Băng Thông**
| Metric | Test 1 | Test 2 | Đánh Giá |
|--------|--------|--------|-----------|
| Data Received | 76 kB/s | 105 kB/s | ✓ Tăng 38.2% |
| Data Sent | 2.5 kB/s | 5.1 kB/s | ✓ Tăng 104% |

### Phân Tích Hiệu Năng Chi Tiết

#### 1. Cải Thiện
- Throughput tăng từ 29.28 lên 41.18 req/s (tăng 40.6%)
- Median response time giảm từ 15.83s xuống 4.87s (giảm 69.2%)
- Thời gian kết nối giảm từ 1.35ms xuống 607.27µs (giảm 55%)
- Băng thông nhận tăng từ 76 kB/s lên 105 kB/s (tăng 38.2%)

#### 2. Kết Luận

##### Ưu Điểm
- Tăng khả năng xử lý từ 26,354 lên 37,080 requests (tăng 40.7%)
- Giảm thời gian phản hồi trung bình từ 12.95s xuống 9.15s (giảm 29.3%)
- Cải thiện đáng kể thời gian xử lý ở mức tải thông thường

##### Nhược Điểm
- Phát sinh lỗi ở một số requests

{{% notice warning %}}
**Lưu ý khi test:**
1. Thay đổi ALB DNS trong script
2. Đảm bảo security group cho phép traffic
3. Monitor resources trong quá trình test
4. Verify auto scaling behavior
{{% /notice %}}

### Hoàn Thành!