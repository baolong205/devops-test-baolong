# Website giới thiệu cá nhân

Trang portfolio cá nhân bằng HTML, CSS và JavaScript thuần. Giao diện tiếng Việt, responsive trên điện thoại và máy tính, có các mục giới thiệu, thông tin cá nhân, dấu mốc và liên hệ.

## Xem trang web

Mở file `index.html` trực tiếp bằng trình duyệt. Không cần cài đặt dependencies hoặc chạy build.

## Cá nhân hóa nội dung

Mở `index.html` và cập nhật các nội dung mẫu sau:

- Tên, lời giới thiệu và thông tin cá nhân.
- Các mục trong phần “Điều mình đang làm”.
- Ảnh chân dung trong thuộc tính `src` của thẻ `<img>`.
- Địa chỉ email `hello@example.com` trong liên kết `mailto:`.
- Tiêu đề trang và mô tả trong phần `<head>`.

Ảnh chân dung và font chữ được tải từ Unsplash và Google Fonts, vì vậy cần kết nối internet để hiển thị đầy đủ. Nếu muốn trang hoạt động hoàn toàn offline, hãy tải các tài nguyên này về dự án và đổi sang đường dẫn cục bộ.

## Jenkins Pipeline

`Jenkinsfile` checkout source, kiểm tra dependencies, build và deploy website lên GitHub Pages. Đây là website HTML tĩnh nên không có dependencies cần cài; build kiểm tra `index.html`, tạo `dist/` và lưu artifact. Pipeline cần Jenkins agent Linux có Git cùng các lệnh `base64`, `cp`, `find`, `mktemp`, và các plugin **Pipeline**, **Git**, **GitHub Branch Source**, **Credentials Binding**.

### Cấu hình GitHub

1. Trong Jenkins, tạo credential loại **Username with password**: username là tài khoản GitHub, password là personal access token có quyền đọc/ghi nội dung repository, ID là `github-https`. Không lưu token trong source code.
2. Tạo job **Multibranch Pipeline**, kết nối repository `baolong205/devops-test-baolong` và đặt script path là `Jenkinsfile`.
3. Trong GitHub, vào **Settings** → **Pages** → **Build and deployment**, chọn **Deploy from a branch**, nhánh `gh-pages`, thư mục `/(root)`, rồi nhấn **Save**.

Deploy chỉ chạy trên nhánh `main`. Log Jenkins hiển thị `BUILD SUCCESS` khi pipeline thành công hoặc `BUILD FAILED` khi có stage lỗi.

## Cấu trúc

```text
.
├── index.html   # Trang web, gồm HTML, CSS và JavaScript
├── Jenkinsfile  # Pipeline checkout, build và deploy
└── README.md    # Tài liệu dự án
```
