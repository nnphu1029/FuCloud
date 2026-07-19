## [17/07/2026] - Thay đổi phần mềm nền của dịch vụ lưu trữ file

**Mục tiêu hôm nay**

- Thay đổi service lưu trữ file (thay thế FileBrowser).
Lý do: FileBrowser không có khả năng xem file thuộc hệ sinh thái Microsoft Office
nên chuyển sang combo Cloudreve + OnlyOffice.

**Đã thực hiện**

- Xóa LXC cũ (chỉ chạy FileBrowser), tạo LXC mới sạch cho combo Cloudreve + OnlyOffice.
- Chuyển container sang chế độ privileged để tránh lỗi phân quyền khi chạy Docker (nesting).
- Mount ổ HDD ngoài vào container tại /hdd, tách biệt khỏi rootfs (SSD).
- Cài đặt Docker và triển khai 2 container: Cloudreve (port 5212) và OnlyOffice Document
Server (port 8080).
- Cấu hình Storage Policy của Cloudreve trỏ vào /hdd để lưu file người dùng trên HDD
thay vì SSD.
- Cấu hình File Applications (WOPI) trong Cloudreve trỏ tới discovery endpoint của
OnlyOffice để bật preview/edit file Office.

**Vấn đề gặp phải**

- `ERR_BLOCKED_BY_CLIENT`: OnlyOffice editor bị extension uBlock Origin (ad-blocker)
chặn nhầm request, khiến trình soạn thảo không load được.
- `Insufficient storage quota`: dù đã cấu hình đúng Storage Policy, tài khoản vẫn
không upload được — nguyên nhân do Storage Policy chưa được gán (bind) cho user
group nào trong Admin Panel, không phải do thiếu dung lượng đĩa thật.

**Cách giải quyết**

- Tắt tạm ad-blocker / test qua cửa sổ ẩn danh để xác nhận và loại trừ nguyên nhân.
- Vào Admin Panel → Groups, gán Storage Policy vừa tạo cho user group tương ứng.

**Kết quả**

- Đã cài đặt và triển khai thành công 2 Docker container (Cloudreve + OnlyOffice).
- Truy cập được ở mạng nội bộ (local), xem/sửa file Word/Excel/PowerPoint hoạt động
bình thường qua giao diện Cloudreve.

**Việc tiếp theo**

- Expose Cloudreve ra Internet để mọi người có thể sử dụng và chia sẻ.

---

## [18/07/2026] - Expose Cloudreve ra Internet

**Mục tiêu hôm nay**

- Expose dịch vụ Cloudreve+OnlyOffice ra Internet.

**Đã thực hiện**

- Thử mở cổng (Port Forwarding) cho port 5212 (Cloudreve) và 8080 (OnlyOffice).
- Tạo tunnel đến container thông qua 2 dịch vụ: Cloudflared Quick Tunnel và Tailscale Funnel.
- Cấu hình Tailscale Funnel với 2 port riêng (443 cho Cloudreve, 8443 cho OnlyOffice)
thay vì gộp chung 1 path, do OnlyOffice tự sinh sai WOPI action URL khi bị đặt dưới sub-path.
- Cập nhật lại Site URL (Cloudreve) và Discovery URL (OnlyOffice WOPI) trỏ theo link Funnel.

**Vấn đề gặp phải**

- `Cannot forward port`: nhà mạng (FPT) dùng CGNAT nên không thể mở cổng ở nhà.
- `Link (Cloudflared Quick Tunnel) changes on every reboot`: CQT sinh link mới mỗi lần restart,
phải cập nhật thủ công 2 chỗ cấu hình (Site URL + Discovery URL) sau mỗi lần đó.
- `404 Not Found` khi gọi discovery URL: do gộp Cloudreve + OnlyOffice chung 1 hostname qua sub-path `/office`, khiến OnlyOffice tự sinh action URL thiếu tiền tố path.
- `ERR_BLOCKED_BY_CLIENT`: ad-blocker trên trình duyệt chặn nhầm request của OnlyOffice editor.

**Cách giải quyết**

- Quyết định không mở cổng, dùng tunnel thay thế.
- Cân nhắc viết script tự động cập nhật link cho CQT, nhưng cần API nội bộ (không có tài liệu
công khai) của Cloudreve để re-import WOPI config → quá phức tạp, từ bỏ hướng này.
- Chuyển hẳn sang Tailscale Funnel vì link cố định vĩnh viễn, không cần script tự động cập nhật.
- Tách Cloudreve và OnlyOffice ra 2 port Funnel riêng (443 / 8443) thay vì gộp path.
- Test qua ad-blocker: whitelist domain / dùng incognito để loại trừ lỗi ERR_BLOCKED_BY_CLIENT.

**Kết quả**

- Đã expose thành công Cloudreve + OnlyOffice ra Internet qua Tailscale Funnel,
link cố định không đổi khi container reboot. Xem/sửa file Word/Excel/PowerPoint
hoạt động bình thường từ mạng ngoài.

**Việc tiếp theo**

- Viết README cho repo.
- Cân nhắc bật lại JWT cho OnlyOffice (hiện đang tắt) nếu tìm được cách cấu hình phía Cloudreve.
- Backup định kỳ /opt/cloudreve/data.