# Đề 3: Website Quảng bá Sản phẩm (WordPress)

**Mã sinh viên:** DTC245200098

**Website thực hiện:** Quảng bá sản phẩm Torriden

## 1. Giới thiệu

Dự án triển khai website quảng bá sản phẩm Torriden bằng WordPress trên máy ảo Ubuntu, sử dụng Docker Compose để quản lý các dịch vụ.

Hệ thống tích hợp cơ sở dữ liệu MySQL, phpMyAdmin, Nginx reverse proxy, Prometheus, Grafana, Loki và Promtail nhằm đáp ứng các yêu cầu triển khai, giám sát, thu thập nhật ký và bảo mật.

## 2. Công nghệ và kiến trúc hệ thống

| Thành phần | Chức năng |
|---|---|
| Ubuntu, VMware | Môi trường triển khai |
| Docker Compose | Quản lý dịch vụ container |
| WordPress | Website quảng bá sản phẩm |
| MySQL | Lưu trữ dữ liệu |
| phpMyAdmin | Quản trị MySQL |
| Nginx | Reverse proxy |
| Prometheus | Thu thập metrics |
| Grafana | Dashboard giám sát và xem log |
| Loki | Lưu trữ, truy vấn log |
| Promtail | Thu thập log Docker |

Luồng hoạt động chính:

- Người dùng → Nginx → WordPress → MySQL.
- Prometheus → Thu thập metrics → Grafana hiển thị dashboard.
- Docker logs → Promtail → Loki → Grafana Explore.

Hệ thống sử dụng các mạng Docker `web_net`, `db_net`, `monitor_net` và một mạng giám sát bên ngoài.

## 3. Yêu cầu trước khi triển khai

Máy Ubuntu cần có:

- Docker Engine.
- Docker Compose plugin.
- Git.
- Kết nối mạng để tải Docker images.
- Các cổng cần thiết chưa bị ứng dụng khác sử dụng.

Kiểm tra công cụ:

```bash
docker --version
docker compose version
git --version
```

## 4. Tải mã nguồn từ GitHub

```bash
git clone https://github.com/dtc245200098-beep/DTC245200098.git
cd DTC245200098
```

Các thư mục cấu hình chính:

```text
DTC245200098/
├── compose.yaml
├── nginx/
│   └── wordpress.conf
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   ├── dashboards/
│   │   └── wordpress-dashboard.json
│   └── provisioning/
├── loki/
│   └── loki-config.yml
├── promtail/
│   └── promtail-config.yml
├── docs/
│   └── logging.md
├── .gitignore
└── README.md
```

## 5. Cấu hình môi trường

Tại thư mục dự án, tạo file `.env`:

```bash
nano .env
```

Khai báo hai biến mật khẩu:

```dotenv
MYSQL_ROOT_PASSWORD=THAY_BANG_MAT_KHAU_ROOT_MANH
MYSQL_PASSWORD=THAY_BANG_MAT_KHAU_WORDPRESS_MANH
```

Thay các giá trị mẫu bằng mật khẩu riêng, đủ mạnh. Không sử dụng nguyên văn mật khẩu mẫu.

Hạn chế quyền truy cập:

```bash
chmod 600 .env
```

Không đưa `.env` lên GitHub. Kiểm tra `.gitignore` trước khi commit.

## 6. Chuẩn bị mạng Docker

Cấu hình Compose sử dụng mạng bên ngoài `monitor_net_dtc245200098`.

Kiểm tra mạng:

```bash
docker network inspect monitor_net_dtc245200098
```

Nếu mạng chưa tồn tại, tạo mạng:

```bash
docker network create monitor_net_dtc245200098
```

Các mạng nội bộ còn lại được Docker Compose quản lý.

## 7. Khởi chạy hệ thống

Kiểm tra cấu hình:

```bash
docker compose config --quiet
```

Tải và khởi chạy các dịch vụ:

```bash
docker compose up -d
```

Kiểm tra trạng thái:

```bash
docker compose ps
```

Các dịch vụ dự kiến gồm Nginx, MySQL, WordPress, phpMyAdmin, Prometheus, Grafana, Loki và Promtail.

Lần triển khai mới, WordPress có thể yêu cầu hoàn tất cài đặt ban đầu trên trình duyệt.

## 8. Địa chỉ truy cập

Với cấu hình hiện tại:

| Dịch vụ | Địa chỉ |
|---|---|
| Website qua Nginx | `http://IP_UBUNTU:8083` |
| WordPress trực tiếp trên Ubuntu | `http://127.0.0.1:8082` |
| phpMyAdmin trên Ubuntu | `http://127.0.0.1:8081` |
| Prometheus | `http://IP_UBUNTU:9091` |
| Grafana | `http://IP_UBUNTU:3001` |
| Loki API trên Ubuntu | `http://127.0.0.1:3101` |

`IP_UBUNTU` là địa chỉ IP thực tế của máy ảo. Kiểm tra bằng:

```bash
hostname -I
```

Các dịch vụ chỉ gắn với `127.0.0.1` không thể truy cập trực tiếp từ máy khác qua IP LAN nếu không cấu hình phương thức truy cập phù hợp.

## 9. Kiểm tra WordPress và cơ sở dữ liệu

Truy cập website qua Nginx và kiểm tra giao diện quảng bá sản phẩm.

Mở phpMyAdmin để kiểm tra cơ sở dữ liệu `wordpress` và các bảng dữ liệu.

Nếu chưa truy cập được website, kiểm tra:

```bash
docker compose ps
docker compose logs --tail=50 nginx wordpress mysql
```

## 10. Kiểm tra Nginx reverse proxy

Nginx sử dụng cấu hình tại:

`nginx/wordpress.conf`

Trên máy Ubuntu, kiểm tra HTTP headers:

```bash
curl -I http://127.0.0.1:8083
```

Kiểm tra cấu hình bên trong container:

```bash
docker compose exec nginx nginx -t
```

Kiểm tra các security headers theo nội dung cấu hình Nginx thực tế.

## 11. Giám sát Prometheus và Grafana

Mở Prometheus tại cổng `9091`, vào **Status → Targets** để kiểm tra các mục tiêu thu thập metrics.

Mở Grafana tại cổng `3001`, đăng nhập và truy cập dashboard **WordPress System Monitoring**.

Dashboard của dự án được lưu tại:

`grafana/dashboards/wordpress-dashboard.json`

Cấu hình provisioning nằm trong:

`grafana/provisioning/`

**Lưu ý:** Một số exporter có thể được triển khai riêng trên máy chủ và không nằm trong tám dịch vụ của `compose.yaml`. Để tái tạo đầy đủ dashboard trên máy mới, cần chuẩn bị các exporter và địa chỉ targets tương ứng.

## 12. Hệ thống log tập trung Loki và Promtail

Promtail thu thập nhật ký Docker và gửi đến Loki.

Trong Grafana, mở **Explore**, chọn Loki và thực hiện các truy vấn LogQL.

Xem toàn bộ log có nhãn container:

```logql
{container=~".+"}
```

Lọc log liên quan các container có tên chứa `wordpress`:

```logql
{container=~".*wordpress.*"}
```

Tìm log chứa từ khóa `error`:

```logql
{container=~".+"} |= "error"
```

Các truy vấn đã được kiểm thử. Chi tiết xem tại [docs/logging.md](docs/logging.md).

## 13. Bảo mật hệ thống

Các biện pháp đã cấu hình và kiểm tra trong quá trình triển khai:

- Tách mạng Docker theo chức năng.
- Đặt `db_net` ở chế độ `internal: true`.
- Lưu mật khẩu trong `.env`, hạn chế quyền truy cập.
- Không công khai `.env` trong repository.
- Cấu hình Nginx security headers.
- Sử dụng UFW để kiểm soát truy cập mạng.

Việc áp dụng quyền non-root và các biện pháp bảo mật bổ sung cần được kiểm tra theo từng container trước khi xác nhận hoàn tất.

## 14. Xử lý sự cố và quản lý dịch vụ

Kiểm tra các container:

```bash
docker compose ps
```

Xem nhật ký một dịch vụ:

```bash
docker compose logs --tail=100 wordpress
```

Khởi động lại một dịch vụ:

```bash
docker compose restart wordpress
```

Dừng các container của dự án:

```bash
docker compose down
```

Không sử dụng `docker compose down -v` khi cần giữ dữ liệu WordPress, MySQL và Grafana, vì tùy chọn `-v` có thể xóa các Docker volumes.

## 15. Quản lý mã nguồn GitHub

Repository:

https://github.com/dtc245200098-beep/DTC245200098

Các mốc commit chính:

- **Commit 1:** Nginx reverse proxy và network isolation.
- **Commit 2:** Prometheus và Grafana monitoring.
- **Commit 3:** Tài liệu kiểm thử Loki, Promtail và LogQL.

Các file cấu hình và tài liệu được quản lý trên GitHub để phục vụ kiểm tra, triển khai và báo cáo môn học.
