# 🛠️ Hướng Dẫn Khắc Phục Lỗi Cài Đặt Google Workspace CLI

> **Phạm vi áp dụng:** Windows | **Công cụ:** Google Workspace CLI (`gws`)

Tài liệu này hướng dẫn cách xử lý các lỗi phổ biến khi cài đặt Google Workspace CLI qua NPM trên Windows, đặc biệt là lỗi liên quan đến quyền truy cập (`EPERM`) hoặc hệ thống không tìm thấy PowerShell (`ENOENT`).

---

## 📋 Mục Lục

1. [Dấu hiệu lỗi](#1-dấu-hiệu-lỗi)
2. [Cách 1: Cập nhật PATH cho PowerShell](#2-cách-1-cập-nhật-path-cho-powershell)
3. [Cách 2: Cài đặt thủ công](#3-cách-2-cài-đặt-thủ-công)
4. [Lưu ý quan trọng](#4-lưu-ý-quan-trọng)

---

## 1. Dấu Hiệu Lỗi

Khi chạy lệnh cài đặt:

```bash
npm install -g @googleworkspace/cli
```

Bạn có thể gặp một trong các thông báo lỗi sau:

| Mã lỗi | Thông báo | Nguyên nhân |
|--------|-----------|-------------|
| `ENOENT` | `npm error Error fetching release: spawnSync powershell.exe ENOENT` | Hệ thống không tìm thấy `powershell.exe` trong PATH |
| `EPERM` | `npm warn cleanup [Error: EPERM: operation not permitted, rmdir ...]` | Thiếu quyền Administrator khi ghi file |

---

## 2. Cách 1: Cập Nhật PATH cho PowerShell

> ✅ **Đây là cách khắc phục được khuyến nghị.** Áp dụng khi script cài đặt không tìm thấy `powershell.exe` của Windows.

### Các Bước Thực Hiện

**Bước 1 — Mở cài đặt biến môi trường:**

1. Nhấn phím `Windows`, gõ **`env`**
2. Chọn **"Edit the system environment variables"**
3. Nhấn nút **Environment Variables...** ở góc dưới bên phải

**Bước 2 — Chỉnh sửa biến `Path`:**

1. Tại mục **System variables** (bảng phía dưới), tìm dòng **`Path`** → nhấn **Edit**
2. Nhấn **New** và dán đường dẫn sau:
   ```
   C:\Windows\System32\WindowsPowerShell\v1.0\
   ```
3. Nhấn **Move Up** để đưa dòng này lên vị trí ưu tiên cao hơn

**Bước 3 — Lưu và áp dụng:**

- Nhấn **OK** để đóng tất cả các cửa sổ dialog

**Bước 4 — Khởi động lại Terminal và cài lại:**

1. Đóng toàn bộ cửa sổ CMD / PowerShell đang mở
2. Mở **Command Prompt** hoặc **PowerShell** mới với quyền **Run as Administrator**
3. Chạy lại lệnh:
   ```bash
   npm install -g @googleworkspace/cli
   ```

---

## 3. Cách 2: Cài Đặt Thủ Công

> 💡 **Dùng khi:** Môi trường NPM bị hạn chế bởi chính sách bảo mật công ty, tường lửa, hoặc lỗi mạng không ổn định.

### Các Bước Thực Hiện

**Bước 1 — Tải tệp thực thi:**

1. Truy cập trang phát hành chính thức:
   👉 [Google Workspace CLI Releases](https://github.com/googleworkspace/cli/releases)
2. Tải file `.zip` phù hợp với Windows:
   ```
   google-workspace-cli-x86_64-pc-windows-msvc.zip
   ```

**Bước 2 — Giải nén:**

- Giải nén file `.zip` vừa tải — bạn sẽ nhận được file thực thi **`gs.exe`**

**Bước 3 — Thiết lập quyền truy cập toàn cục:**

Chọn **một** trong hai cách sau:

- **Cách A *(Nhanh nhất)*:** Copy `gs.exe` trực tiếp vào:
  ```
  C:\Windows\System32\
  ```

- **Cách B *(Khuyên dùng)*:**
  1. Tạo thư mục `C:\bin` (hoặc tên tùy ý)
  2. Copy `gs.exe` vào thư mục đó
  3. Thêm `C:\bin` vào biến môi trường **`Path`** (tham khảo [Cách 1, Bước 2](#bước-2--chỉnh-sửa-biến-path))

**Bước 4 — Kiểm tra cài đặt:**

Mở terminal mới và chạy:

```bash
gs --version
```

Nếu hiển thị số phiên bản → cài đặt thành công! 🎉

---

## 4. ⚠️ Lưu Ý Quan Trọng

| # | Vấn đề | Giải pháp |
|---|--------|-----------|
| 1 | **Quyền Admin** | Luôn chạy CMD / PowerShell bằng **Run as Administrator** khi cài đặt |
| 2 | **Phần mềm diệt virus** | Một số Antivirus có thể chặn lệnh `spawnSync`. Nếu lỗi vẫn tiếp diễn, hãy **tạm tắt Antivirus** trong quá trình cài đặt, sau đó bật lại |
| 3 | **Khởi động lại** | Sau khi thay đổi biến môi trường PATH, **bắt buộc phải mở terminal mới** để áp dụng thay đổi |

---

* Tài liệu dùng cho GDG Cloud Đà Nẵng — Build with AI 2026