# RUN_COMPOSE.md – Hướng dẫn vận hành Hệ thống Team Core (Lab 05)

Tài liệu này hỗ trợ các thành viên khác hoặc Giảng viên khởi chạy nhanh toàn bộ Stack dịch vụ phối hợp (Core API + DB + AI Service) thông qua Docker Compose.

---

## 1. Khởi tạo môi trường nền

```bash
# 1. Tạo mạng ảo chung cho toàn lớp (chỉ làm lần đầu)
docker network create class-net

# 2. Khởi tạo file biến môi trường cục bộ
cp .env.example .env

# 3. Cài đặt Node dependencies phục vụ Newman testing
npm install
```

---

## 2. Kích hoạt Stack bằng Docker Compose

Sử dụng lệnh sau để tự động build, thiết lập mạng cục bộ `team-internal` và kết nối các service:

```bash
docker compose up -d --build
```

**Kiểm tra trạng thái Sẵn sàng (Readiness Checklist):**
```bash
docker compose ps
```
> **Lưu ý:** Đảm bảo tất cả các service (`fit4110-core-api`, `fit4110-core-db`, `fit4110-core-ai`) đều hiển thị trạng thái `Up ... (healthy)` tại terminal trước khi thực hiện test.

---

## 3. Kiểm tra nhanh Endpoint Health Check

```bash
# API Gateway Endpoint
curl http://localhost:8000/health

# AI Service Endpoint (Internal Check)
docker compose exec ai-service curl http://localhost:9000/health
```

---

## 4. Chạy Automated Newman Test

Kích hoạt bộ test Postman collection để kiểm thử tích hợp End-to-End:

```bash
npm run test:local
```

Kết quả báo cáo tự động lưu trữ tại thư mục `reports/`.

---

## 5. Giải tán hạ tầng

Khi không sử dụng, dọn dẹp các container nhưng vẫn giữ lại cấu trúc dữ liệu lưu trong Volume:

```bash
docker compose down
```
*(Nếu muốn xóa sạch toàn bộ dữ liệu của Database làm lại từ đầu, chạy lệnh: `docker compose down -v`)*