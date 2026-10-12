# Báo cáo: Tối ưu tốc độ CI bằng Gradle Dependency Caching

## 1. Mục tiêu
Áp dụng tính năng caching cho Gradle để giảm thiểu thời gian tải lại các thư viện dependency (Internet fetching) trong các luồng CI/CD trên GitHub Actions.

## 2. Kết quả thử nghiệm

**Lần Push 1 (Khởi tạo Cache - Không có sẵn Cache):**
- Trạng thái bước `setup-java`: Cài đặt JDK và tiến hành lưu cache sau khi Job hoàn thành.
- Thời gian chạy bước `setup-java`: `[Điền số phút/giây, VD: 15s]`
- Thời gian chạy bước `./gradlew test`: `[Điền số phút/giây, VD: 2m 10s]` (Do Gradle phải tải toàn bộ dependencies từ Maven Central/JCenter).
- Tổng thời gian chạy Workflow: `[Điền tổng thời gian, VD: 2m 45s]`

**Lần Push 2 (Sử dụng Cache):**
- Trạng thái bước `setup-java`: Log hiển thị `Cache restored successfully from key: gradle-...`
- Thời gian chạy bước `setup-java`: `[Điền số phút/giây, VD: 2s]`
- Thời gian chạy bước `./gradlew test`: `[Điền số phút/giây, VD: 35s]` (Không mất thời gian tải lại thư viện, chỉ tốn thời gian compile và run test).
- Tổng thời gian chạy Workflow: `[Điền tổng thời gian, VD: 50s]`

## 3. Kết luận
- Việc bổ sung tham số `cache: 'gradle'` vào action `setup-java@v5` giúp workflow lưu trữ thư mục `~/.gradle/caches` lại trên GitHub Actions server.
- Ở các lần chạy sau, thay vì tốn thời gian và băng thông mạng để tải lại các thư viện Java/Spring Boot, GitHub Actions chỉ cần giải nén cache có sẵn, giúp **thời gian chạy CI giảm đi đáng kể** (khoảng `[Điền % hoặc số phút chênh lệch]`), tối ưu hóa tài nguyên và tăng tốc độ phản hồi cho quy trình phát triển.

- chốt oke