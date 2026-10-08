# WebFM Lab — Tái lập ý tưởng truy cập Web và ChatGPT qua FM + SMS

**Website:** https://base27-cvnss.github.io/webfm/ (sau khi GitHub Pages deploy thành công).

Bản bổ sung gồm một trang tương tác **offline-first** mô phỏng SMS uplink, khung dữ liệu C137/500 byte, mất khung, Browser / ChatGPT / Knowledge Hub. **Demo không thu sóng FM, gửi SMS thật hoặc gọi OpenAI API.**

**Cấu trúc:**
- \`docs/\`: giao diện WebFM Lab, JavaScript mô phỏng, service worker cache offline.
- \`server/\`: server Python nghiên cứu (SMS, Selenium, OpenAI API, Quiet encoder).
- \`android-app/\`: ứng dụng Android nghiên cứu (SMS helper, FM receiver, Quiet decoder).
- \`DEPLOYMENT.md\`: hướng dẫn phân biệt demo và các điều kiện cần có để làm hệ thống thật.
- \`.github/workflows/pages.yml\`: GitHub Pages CI (deploy \`docs/\`).

## Chạy local

\`\`\`sh
python3 -m http.server 8080 --directory docs
# Truy cập http://localhost:8080
\`\`\`

## Hệ thống thực tế

\`\`\`text
Android -- SMS: REQ:https://... / GPT:... --> Gateway SMS dongle
                                                    |
                                            Selenium / OpenAI API
                                                    |
                                    SonicEncoder (.sonic) / Quiet WAV
                                                    |
                                  Licensed FM Station --> Android FM tuner
                                                          |
                                                 Quiet decode / SQLite
\`\`\`

Gateway cần kết nối Internet riêng để lấy website hoặc gọi API, dù người dùng không cần Internet di động. Thiết bị Android phải có FM hardware/driver truy cập luồng âm thanh, nghiên cứu sử dụng LineageOS đã chỉnh sửa và tai nghe dây làm anten. FM broadcast cần vận hành theo quy định tần số vô tuyến. Không đưa \`OPENAI_API_KEY\` vào mã GitHub Pages. SMS và FM không mặc định bảo mật cho nội dung nhạy cảm.

Nghiên cứu: Ayush Pandey et al., *Tuning into the Web: A Low-Cost Access Solution for Developing Countries*, ACM SIGCOMM 2026. DOI: https://doi.org/10.1145/3789240.3829209. Code gốc: https://github.com/ayushpandeynp/webfm.

**Từ bài báo (không phải benchmark website):** 10 kbps FM downlink, 92 OFDM subcarriers, center 9.2 kHz, frame 500 bytes, thử nghiệm sáu tuần / 30 người tại Cameroon.

**Bản quyền:** Repository nguồn mang giấy phép MIT © 2024 Ayush Pandey. Giữ nguyên tệp LICENSE và ghi công tác giả gốc.
