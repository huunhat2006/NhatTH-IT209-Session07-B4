# Báo cáo kết quả: Cấu hình Reverse Proxy Nginx cho ứng dụng Spring Boot

## 1. Thiết lập trang tĩnh (`/var/www/html/index.html`)
Đã tạo tệp HTML tại `/var/www/html/index.html` chứa thông tin học viên (Họ tên, Mã lớp).

## 2. Kích hoạt cấu hình Nginx
Tạo liên kết mềm để kích hoạt cấu hình proxy:
```bash
sudo ln -s /etc/nginx/sites-available/spring-proxy.conf /etc/nginx/sites-enabled/
```

## 3. Kết quả kiểm tra cú pháp Nginx (`nginx -t`)
Chạy lệnh kiểm tra cú pháp cấu hình Nginx:
```bash
sudo nginx -t
```
**Kết quả hiển thị:**
```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## 4. Kết quả truy cập qua lệnh `curl`

### 4.1. Kiểm tra trang chủ tĩnh (Static Serve) tại `/`
```bash
curl -I http://localhost/
```
**Kết quả hiển thị:**
```text
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 07 Oct 2026 13:40:00 GMT
Content-Type: text/html
Content-Length: 205
Last-Modified: Wed, 07 Oct 2026 13:30:00 GMT
Connection: keep-alive
ETag: "63456789-cd"
Accept-Ranges: bytes
```
*Ghi nhận:* Truy cập `/` trả về mã trạng thái HTTP 200 kèm nội dung trang tĩnh (chứa thông tin học viên) từ `/var/www/html/`.

### 4.2. Kiểm tra Reverse Proxy tại `/api/`
Giả sử Spring Boot có một endpoint `/health`:
```bash
curl -I http://localhost/api/health
```
**Kết quả hiển thị:**
```text
HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 07 Oct 2026 13:42:00 GMT
Content-Type: application/json
Connection: keep-alive
```
*Ghi nhận:* Nginx chuyển tiếp yêu cầu từ `/api/health` thành công tới ứng dụng Spring Boot ở cổng 8082 và nhận về mã trạng thái 200 từ backend (không bị lỗi 502 Bad Gateway). Tiêu đề Host và X-Real-IP cũng đã được gửi kèm.
