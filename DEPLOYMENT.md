# WebFM — hướng dẫn tái lập thực địa

## Phân biệt ba lớp

| Chức năng | Website GitHub Pages | Gateway Python gốc | Android gốc |
|---|---|---|---|
| Browser / ChatGPT / Knowledge Hub | Mô phỏng offline | Xử lý nội dung | Giao diện thật |
| Lệnh REQ: / GPT: | Hiển thị và mở sms: URI | Nhận SMS dongle | SmsManager gửi SMS |
| Gọi OpenAI API | Không | Có khi cài API key | Nhận phản hồi FM |
| Truy cập website Internet | Không | Selenium + Chrome | Nhận screenshot |
| OFDM + radio FM | Không | Quiet encoder → WAV; cần trạm phát | FM chip + ROM + decoder |
| Mất gói | Ngẫu nhiên (giả lập) | Đầu phát không mô phỏng đường truyền | CRC/FEC, nội suy tùy cấu hình |

## Quy trình kết nối

1. Lắp gateway tại đài: máy chủ có Internet, Python/Docker/Chrome tương thích, dongle SIM hỗ trợ SMS, ngõ đưa âm thanh WAV đến đài FM.
2. Trong \`server/config.py\`, \`REQUEST_METHOD\` mặc định là \`api_call\`; đổi \`sms\` khi đã kiểm chứng dongle. Tần số, giờ phát và tọa độ hiện tại là thông số thí nghiệm Cameroon, không áp dụng trực tiếp cho Việt Nam.
3. Trên gateway cấu hình \`OPENAI_API_KEY\`, \`DONGLE_PASSWORD\` bằng biến môi trường (không commit vào Git).
4. Kiểm tra luồng SMS → \`server/SMS.py\` → \`server/Manager.py\` → \`server/ScreenshotQueue.py\` / \`server/ChatGPT.py\` → \`server/SonicEncoder.py\` → \`server/PlayerQueue.py\`.
5. Test loopback với audio/Quiet trước khi dùng vô tuyến. Để phát thật, làm việc với đài FM được cấp phép và đo RSSI, tỉ lệ mất khung, thời gian phát, độ chính xác khôi phục.
6. Trên Android, chip FM phải truy cập được audio vào ứng dụng. Nghiên cứu sửa LineageOS để ứng dụng nhận PCM và dùng tai nghe dây làm anten.
7. Cần kiểm thử bảo mật: lệnh SMS phải giới hạn tần suất, xác thực request, URL allowlist chống SSRF trên gateway, log tránh thông tin cá nhân, giới hạn prompt, mã hóa và ký số khi truyền riêng tư.

**Không công khai \`server/API.py /new-sms\` hiện tại**: route này dùng thử nghiệm và chưa có xác thực/rate limiting. Hạn chế truy cập localhost hoặc mạng thí nghiệm cho tới khi harden.

## Web page deployment

Workflow \`.github/workflows/pages.yml\` triển khai \`docs/\` khi push nhánh master. Bật *Settings → Pages → Source: GitHub Actions*, rồi xem trạng thái trong *Actions*. Chỉ xác nhận URL https://base27-cvnss.github.io/webfm/ hoạt động sau khi deployment thành công.

## Nguồn

Ayush Pandey, Rohail Asim, Jean Louis K. E. Fendji, Talal Rahwan, Matteo Varvello, Yasir Zaki (2026). *Tuning into the Web: A Low-Cost Access Solution for Developing Countries*, ACM SIGCOMM 2026. https://doi.org/10.1145/3789240.3829209.

Thông số 10 kbps, OFDM 92 subcarriers và 500-byte frame là từ bài báo; chưa được tái đo trên hạ tầng người dùng.
