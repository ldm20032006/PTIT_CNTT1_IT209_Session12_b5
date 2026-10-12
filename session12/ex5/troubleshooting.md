# Bài 5: Chẩn đoán và khắc phục sự cố CI/CD Pipeline

## 1. Mục tiêu

Phân tích và sửa 3 lỗi trong GitHub Actions Pipeline để đảm bảo quá trình Build, Test và Docker Image hoạt động thành công.

## 2. Các lỗi và phương pháp khắc phục

### Lỗi 1: Permission Denied

**Nguyên nhân:** File `gradlew` chưa được cấp quyền thực thi trên Linux Runner.

**Cách khắc phục:**

```bash
chmod +x ./gradlew
```

Thực hiện cấp quyền trước khi chạy:

```bash
./gradlew clean build
```

### Lỗi 2: Authentication Failed

**Nguyên nhân:** Workflow sử dụng sai tên GitHub Secret để xác thực với Docker Registry.

**Cách khắc phục:**

Sử dụng đúng tên Secret:

```yaml
password: ${{ secrets.REGISTRY_TOKEN }}
```

Đảm bảo Secret `REGISTRY_TOKEN` tồn tại và chứa token hợp lệ có quyền truy cập GHCR.

### Lỗi 3: Build Context Error

**Nguyên nhân:** Docker Build không tìm thấy Dockerfile tại đường dẫn được cấu hình.

**Cách khắc phục:**

```yaml
context: .
file: ./Dockerfile
```

Kiểm tra Dockerfile tồn tại ở thư mục gốc repository.

### Lỗi 4: Gradle Wrapper JAR không hợp lệ

**Nguyên nhân:** `gradle/actions/setup-gradle@v4` kiểm tra các Gradle Wrapper JAR trong repository. JAR có kích thước 0 byte không khớp với checksum của wrapper chính thức và làm bước setup thất bại.

**Cách khắc phục:** Khôi phục wrapper từ một bản Gradle chính thức; không tắt kiểm tra wrapper. Dự án Gradle nằm trong `session12/ex5`, vì vậy các lệnh Gradle trong workflow phải chạy từ thư mục đó. Wrapper hiện dùng Gradle 8.14.3, tương thích với Java 21.

## 3. Quy trình kiểm tra

1. Sửa file `.github/workflows/broken-pipeline.yml`.
2. Commit và push lên nhánh `main`.
3. Mở tab Actions trên GitHub.
4. Chọn workflow `Fix Broken CI/CD Pipeline`.
5. Kiểm tra log từng bước Build, Test, Login và Docker Build.
6. Xác nhận toàn bộ pipeline chạy thành công.

## 4. Kết quả

- Lỗi quyền thực thi Gradle Wrapper: Chờ xác nhận trên GitHub Actions.
- Lỗi đăng nhập Docker Registry: Chờ xác nhận trên GitHub Actions.
- Lỗi Docker Build Context: Chờ xác nhận trên GitHub Actions.
- Trạng thái Pipeline: Chờ chạy kiểm thử.

## 5. Minh chứng

Chèn ảnh chụp màn hình GitHub Actions hiển thị workflow tích xanh sau khi kiểm thử thành công.

## 6. Kết luận

Bài thực hành giúp hiểu cách phân tích log, xác định nguyên nhân và khắc phục các lỗi thường gặp trong quy trình CI/CD sử dụng GitHub Actions, Gradle và Docker.