---
title : "Chạy Project ở Local"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Bước 1 — Khởi động dev server

```bash
npm run dev
```

Mở trình duyệt tại **http://localhost:5173**.

![vite dev server](/images/5-Workshop/5.4-Deploy-frontend/vite-dev.png)

#### Bước 2 — Xác nhận trang đăng nhập hiển thị

Bạn sẽ thấy màn hình đăng nhập của giao diện 3D glass. Nếu trang không load được, kiểm tra lại `frontend/.env` (bước trước) và đảm bảo backend đã PASS hết ở [5.3.4](../../5.3-Deploy%20Backend/5.3.4-Verify%20Backend/).

![login page](/images/5-Workshop/5.4-Deploy-frontend/login-page.png)