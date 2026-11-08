# 💛 Locket Gold VIP - Shadowrocket Module & NextDNS

[![Shadowrocket](https://img.shields.io/badge/Shadowrocket-Module-blue.svg)](https://apps.apple.com/app/shadowrocket/id932747118)
[![iOS Support](https://img.shields.io/badge/iOS-14.0+-black.svg?logo=apple)](https://apple.com)
[![NextDNS](https://img.shields.io/badge/NextDNS-Profile%20a2dd89-brightgreen.svg)](https://apple.nextdns.io/?profile=a2dd89)
[![Maintainer](https://img.shields.io/badge/Credit%20%26%20Mod-RealTrueHieu-yellow.svg)](https://github.com/RealTrueHieu)

Kho lưu trữ Module **Shadowrocket** và cấu hình **NextDNS** giúp mở khóa toàn diện các tính năng cao cấp của **Locket Gold (iOS)**: hỗ trợ bộ lọc AI Filter, Photobooth liên hoàn 4 ảnh, video dài tối đa 45s, huy hiệu VIP profile và gỡ bỏ quảng cáo.

---

## 📌 1. Link Cài Đặt Nhanh

| Thành phần | Link Trực Tiếp / Raw URL |
| :--- | :--- |
| 🚀 **Shadowrocket Module** | `https://raw.githubusercontent.com/RealTrueHieu/Locket-Gold/main/Locket-Gold.module` |
| 🛡️ **NextDNS Profile Web** | [https://apple.nextdns.io/?profile=a2dd89](https://apple.nextdns.io/?profile=a2dd89) |
| 📲 **NextDNS MobileConfig File** | [Tải file .mobileconfig](https://raw.githubusercontent.com/RealTrueHieu/Locket-Gold/main/DNS/NextDNS_LocketGold.mobileconfig) |

---

## ✨ 2. Tính Năng Chính

- 🔓 **Kích hoạt Locket Gold vĩnh viễn**: Mở khóa gói thuê bao `locket_1600_1y` thông qua can thiệp RevenueCat API.
- 🎬 **Hỗ trợ video 45 giây**: Vượt qua giới hạn mặc định của Locket (10s/15s) lên đến 45 giây.
- 🎨 **Mở khóa trọn bộ Filter AI & Photobooth 4 ảnh**: Tự động chuyển đổi các filter VIP, unblur ảnh và giả lập luồng chụp photobooth 4 bước không lỗi 422.
- 👑 **Huy hiệu Profile Gold & Mạng bạn bè VIP**: Tự động gán huy hiệu vương miện / tim vàng cho tài khoản chính và bạn bè trong hệ thống.
- 🔔 **Thông báo & Pop-up Kích hoạt có Credit**: Hiển thị bảng chào mừng Gold Popup và push notification ghi nhận Credit: **RealTrueHieu**.
- 🚫 **Chặn Sentry & Telemetry**: Chặn triệt để tracking báo lỗi, chống phát hiện can thiệp và tránh xung đột cache.

---

## 🔬 3. Nghiên Cứu Kỹ Thuật (Technical Analysis)

### 3.1. Cơ chế hoạt động của Module Shadowrocket
Module này hoạt động theo 5 tầng can thiệp lưu lượng mạng trên iOS:

```
[Locket App]
    │
    ├── 1. Rule (UDP/QUIC Reject)
    │      └── Chặn UDP port 443 -> Bắt buộc fallback về TCP/TLS để MITM được
    │
    ├── 2. Header Rewrite (Chống Cache)
    │      └── Xóa ETag, If-None-Match, If-Modified-Since trên RevenueCat & Firebase
    │          (Tránh app nhận response HTTP 304 Not Modified lưu cache Free cũ)
    │
    ├── 3. Host Override
    │      └── firebaseremoteconfigrealtime.googleapis.com -> 0.0.0.0
    │          (Vô hiệu hóa Firebase Realtime Updates ghi đè thiết lập VIP)
    │
    ├── 4. Script Response Rewrite
    │      ├── revenuecat.js: Trả về subscriber.entitlements.Gold hợp lệ + Push Banner
    │      ├── locketfix.js: Bơm Feature Gates (video 45s, popup Gold, captions Pro)
    │      └── locket_filter_resp.js: Gán VIP Badge, unblur ảnh filter
    │
    └── 5. Script Request Mock
           └── locket_pb_req.js: Mock phản hồi Photobooth 4-Shot, chống lỗi HTTP 422
```

1. **Chặn UDP/QUIC**: Giao thức QUIC (HTTP/3) mã hóa kết nối dạng UDP khiến các proxy HTTPS MITM không thể can thiệp. Bằng cách `REJECT` cổng UDP tới `api.revenuecat.com` và `firebaseremoteconfig.googleapis.com`, app bắt buộc phải kết nối qua TCP TLS, tạo điều kiện cho Shadowrocket giải mã.
2. **Xóa Header Cache**: Khi iOS đã lưu cache trạng thái tài khoản thường, app sẽ gửi `If-None-Match` hoặc `x-revenuecat-etag`. Máy chủ sẽ trả về `304 Not Modified` mà không có dữ liệu JSON. Header Rewrite xóa các header này để ép máy chủ trả về đầy đủ body `200 OK`.
3. **Cơ chế RevenueCat & Remote Config**:
   - `revenuecat.js` giả lập hóa đơn mua hàng thành công App Store với mã `locket_1600_1y` hết hạn năm `9999`.
   - `locketfix.js` mở khóa cờ tính năng (`video_upload_max_duration: 45`, `camera_roll: enabled`, `custom_captions: enabled`) và hiển thị popup chào mừng **Credit: RealTrueHieu**.

### 3.2. Vai trò của NextDNS Profile `a2dd89`
- **NextDNS** là dịch vụ DNS qua HTTPS (DoH) hoặc TLS (DoT) bảo mật cao cấp.
- URL `https://apple.nextdns.io/?profile=a2dd89` cung cấp profile chuẩn của Apple (`.mobileconfig`) với thông số:
  - DoH Endpoint: `https://apple.dns.nextdns.io/a2dd89`
  - Quy tắc `OnDemandRules`: Tự động kích hoạt trên mọi mạng Wi-Fi và mạng di động (ngoại trừ captive portal và MMS nhà mạng).
- **Tác dụng với Locket**:
  - Tăng tốc độ phân giải tên miền API sạch.
  - Chặn các tracker, telemetry nền của Apple / Google / Sentry thu thập dữ liệu crash.
  - Ngăn ngừa thu hồi chứng chỉ (Anti-revoke) và chặn các server kiểm tra can thiệp từ xa.

---

## 🛠️ 4. Hướng Dẫn Cài Đặt Chi Tiết

### Bước 1: Cài đặt Profile NextDNS (Khuyến nghị)
1. Mở trình duyệt Safari trên iPhone / iPad.
2. Truy cập: [https://apple.nextdns.io/?profile=a2dd89](https://apple.nextdns.io/?profile=a2dd89)
3. Nhập tên thiết bị (ví dụ: `My iPhone`), sau đó ấn nút **Download**.
4. Vào **Cài đặt (Settings)** -> Chọn mục **Đã tải về hồ sơ (Profile Downloaded)** -> Chọn **Cài đặt (Install)**.
5. Sau khi cài xong, kiểm tra tại **Cài đặt** -> **Cài đặt chung** -> **VPN & Quản lý thiết bị** -> **DNS** đã bật `NextDNS (a2dd89)`.

### Bước 2: Thiết lập Shadowrocket & Chứng chỉ CA (HTTPS Decryption)
1. Cài đặt app **Shadowrocket** từ App Store.
2. Mở Shadowrocket -> Vào tab **Cấu hình (Config)**.
3. Nhấn vào mục **HTTPS Decryption**:
   - Bật công tắc **HTTPS Decryption**.
   - Chọn **Tạo chứng chỉ mới (Generate New CA)** -> Chọn **Cài đặt chứng chỉ vào hệ thống (Install to System)**.
4. Mở **Cài đặt iOS**:
   - Vào **Đã tải về hồ sơ** -> Cài đặt chứng chỉ Shadowrocket.
   - Vào **Cài đặt chung** -> **Giới thiệu (About)** -> Cuộn xuống dưới cùng chọn **Cài đặt tin cậy chứng nhận (Certificate Trust Settings)**.
   - Bật công tắc **Tin cậy đầy đủ** cho chứng chỉ Shadowrocket.

### Bước 3: Cài đặt Module Locket Gold
1. Mở Shadowrocket -> Vào tab **Cấu hình (Config)**.
2. Nhấn vào mục **Mô-đun (Modules)** -> Nhấn dấu **+** (Góc trên bên phải).
3. Dán liên kết sau vào ô URL:
   ```
   https://raw.githubusercontent.com/RealTrueHieu/Locket-Gold/main/Locket-Gold.module
   ```
4. Nhấn **Xong (Done)** để Shadowrocket tải module về.
5. Trở lại màn hình chính của Shadowrocket, gạt công tắc kết nối sang **Connected**.

### Bước 4: Tận hưởng Locket Gold
1. Vuốt đóng hẳn ứng dụng Locket (Kill background).
2. Mở lại Locket: Bạn sẽ nhận được thông báo:
   - **Pop-up trong ứng dụng**: *"Đã Kích Hoạt Locket Gold 💛 - Locket Gold by RealTrueHieu"*
   - **Thông báo đẩy (Push Banner)**: *"Locket Gold Active 💛 - Kích hoạt thành công! Credit: RealTrueHieu ✨"*
3. Trải nghiệm chụp ảnh Photobooth, bộ lọc AI, quay video dài tối đa 45s và huy hiệu Gold!

---

## 📂 5. Cấu Trúc Repository

```
Locket-Gold/
├── Locket-Gold.module               # File cấu hình Module chính cho Shadowrocket
├── LocketvfilterV1.module           # Bản sao tên gốc tương thích ngược
├── Module/
│   └── Locket-Gold.module           # Thư mục phụ lưu Module
├── DNS/
│   └── NextDNS_LocketGold.mobileconfig # File cấu hình DNS Apple Profile (a2dd89)
├── js/
│   ├── revenuecat.js                # Xử lý kích hoạt gói Locket Gold + Push notification
│   ├── evenuecat.js                 # Alias tương thích ngược của revenuecat.js
│   ├── locketfix.js                 # Firebase Remote Config VIP, Video 45s & Popup Credit
│   ├── locket_pb_req.js             # Engine Photobooth 4 ảnh & AI Filter
│   ├── locket_change_profile.js     # Đổi Profile, cấp Gold Badge & bạn bè VIP
│   ├── locket_filter_resp.js        # Gỡ lỗi HTTP, unblur ảnh kết quả filter
│   ├── deleteHeader_RC.js           # Xóa cache header RevenueCat
│   └── deleteHeader_Firebase.js     # Xóa cache header Firebase
└── README.md                        # Tài liệu hướng dẫn và phân tích kỹ thuật
```

---

## ⚖️ 6. Tuyên Bố Miễn Trừ Trách Nhiệm (Disclaimer)

- Dự án này được nghiên cứu và phát triển cho mục đích học tập, thử nghiệm và tối ưu hóa trải nghiệm cá nhân về giao thức mạng (MITM & Encrypted DNS).
- Chúng tôi không sở hữu thương hiệu Locket hay dịch vụ RevenueCat. Nếu có điều kiện kinh tế, hãy ủng hộ nhà phát triển ứng dụng bằng cách mua gói bản quyền chính thức trên App Store!

---

**Made with 💛 by [RealTrueHieu](https://github.com/RealTrueHieu)**  
*Credits to NightmarketServer, MinhHiep08 & dhungx for foundational research.*
