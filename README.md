# Codelab GDG Cloud: Tự động hóa với Gemini CLI & Google Workspace CLI

**Ngôn ngữ / Language:** Tiếng Việt (bản này) · [English → README-en.md](README-en.md)

---

Hướng dẫn workshop **Build with AI** kết hợp **Gemini CLI** (mô hình ngôn ngữ) và **Google Workspace CLI (`gws`)** để tự động hóa thao tác trên Gmail, Drive, Calendar và các dịch vụ Workspace khác.

**Mục tiêu:** Thiết lập môi trường, xác thực an toàn, và thực hành các luồng `gws` → `gemini` qua pipeline (`|`).

---

## Mục lục

1. [Yêu cầu hệ thống](#1-yêu-cầu-hệ-thống)
2. [Cài đặt công cụ](#2-cài-đặt-công-cụ)
3. [Workspace CLI: dự án, OAuth và đăng nhập](#3-workspace-cli-dự-án-oauth-và-đăng-nhập)
4. [Kiểm tra kết nối gws](#4-kiểm-tra-kết-nối-gws)
5. [Gemini CLI: API key và đăng nhập](#5-gemini-cli-api-key-và-đăng-nhập)
6. [Bài thực hành (Labs)](#6-bài-thực-hành-labs)
7. [Nộp bài](#7-nộp-bài)

---

## 1. Yêu cầu hệ thống

Áp dụng cho máy chưa cài sẵn Node.js hoặc cần kiểm tra phiên bản.

### 1.1. Node.js (bắt buộc)

`gemini` và `gws` chạy trên nền tảng Node.js.

**Kiểm tra phiên bản**

- **Windows:** mở PowerShell, chạy:

```bash
node -v
```

- **macOS / Linux:** mở Terminal, chạy:

```bash
node -v
```

Nếu hiển thị số phiên bản (ví dụ `v20.x.x`), Node.js đã sẵn sàng.

**Cài đặt (chọn một trong hai cách)**

- **Cách A — qua pnpm (khuyến nghị nếu bạn dùng pnpm):**

  - Windows (PowerShell):

```powershell
Invoke-WebRequest https://get.pnpm.io/install.ps1 -UseBasicParsing | Invoke-Expression
pnpm env use --global lts
```

  - macOS / Linux:

```bash
curl -fsSL https://get.pnpm.io/install.sh | sh -
pnpm env use --global lts
```

- **Cách B — bản cài đặt chính thức:** tải bản **LTS** từ [https://nodejs.org/en/download](https://nodejs.org/en/download) và cài theo hướng dẫn trên trang.

### 1.2. Google Cloud CLI (`gcloud`)

Dùng để làm việc với Google Cloud (dự án, API, OAuth trong luồng `gws`).

1. Làm theo hướng dẫn tại [https://docs.cloud.google.com/sdk/docs/install-sdk](https://docs.cloud.google.com/sdk/docs/install-sdk) cho hệ điều hành của bạn.
2. Sau khi cài, xác minh:

```bash
gcloud --version
```

---

## 2. Cài đặt công cụ

Trong Terminal / PowerShell (sau khi đã có Node.js và `npm`):

```bash
npm install -g @google/gemini-cli
npm install -g @googleworkspace/cli
```

---

## 3. Workspace CLI: dự án, OAuth và đăng nhập

Trên **cùng một Google Cloud project**, làm lần lượt các bước dưới đây. Thứ tự giúp tránh lỗi **403** khi OAuth đang ở chế độ **Testing** (chưa khai báo test user vẫn cố hoàn tất đăng nhập).

### Bước 3.1. Trình thiết lập `gws` (`gws auth setup`)

```bash
gws auth setup
```

Điều hướng bằng phím mũi tên và **Enter**:

| Bước trong tool | Việc cần làm |
|-----------------|--------------|
| 1–2 | Tool kiểm tra `gcloud`; đăng nhập GCP nếu được yêu cầu. |
| 3 (GCP project) | **Create new project** và đặt tên (ví dụ `gdg-cloud-codelab-<tên-bạn>`), hoặc chọn project sẵn có. |
| 4 (Workspace APIs) | Bật API cần thiết; **Space** để chọn thêm Docs, Slides, Forms, Chat nếu cần → **Enter** để enable → chờ hoàn tất. |
| 5 (OAuth credentials) | Khi thấy dạng `Setup complete! Starting 'gws auth login'...`, **tạm dừng** — chuyển **Bước 3.2** khai báo test user **trước** khi hoàn tất đăng nhập OAuth trong trình duyệt. |

### Bước 3.2. Thêm test user (tránh lỗi 403)

Ở chế độ **Testing**, chỉ các Gmail trong **Test users** mới đăng nhập OAuth được.

1. [Google Cloud Console](https://console.cloud.google.com/) → chọn **đúng project** vừa dùng trong `gws auth setup`.
2. **Google Auth platform** (hoặc **APIs & Services** → **OAuth consent screen**) → **Audience** / **Test users**.
3. **Add users** → nhập **chính xác** Gmail dùng cho `gws auth login` → lưu.
4. Xác nhận địa chỉ đã nằm trong danh sách.

### Bước 3.3. Đăng nhập Workspace CLI

```bash
gws auth login
```

Trình duyệt mở — xử lý theo thứ tự:

1. Chọn tài khoản **trùng** test user (Bước 3.2).
2. Cảnh báo **Google hasn't verified this app**: bình thường với client dev / nội bộ → **Continue** (hoặc **Advanced** → **Continue**).
3. Màn hình quyền: chọn scope đủ nhu cầu (workshop thường dùng **Select all**) → **Continue**.
4. Trong terminal, JSON có `"status": "success"` nghĩa là đăng nhập thành công.

---

## 4. Kiểm tra kết nối gws

```bash
gws drive files list
```

Nếu nhận được kết quả (thường là JSON) **không** báo lỗi quyền — kể cả danh sách rỗng — thì `gws` đã hoạt động. Sau đó chuyển sang **mục 5** để cấu hình Gemini cho các lab dùng pipe (`|`).

---

## 5. Gemini CLI: API key và đăng nhập

Gemini CLI dùng **API key** từ Google AI Studio; luồng này **không** thay thế OAuth của `gws` ở mục 3. Hoàn tất cả hai phần trước khi làm lab.

| Bước | Hành động |
|------|-----------|
| 1 | Mở [Google AI Studio — API keys](https://aistudio.google.com/app/apikey), đăng nhập Google, **Create API key**, rồi sao chép key. |
| 2 | Trong terminal, chạy `gemini --approval-mode yolo` (hoặc `gemini` nếu không cần tự động duyệt thao tác trong CLI). Tránh dùng đồng thời `-y`/`--yolo` với `--approval-mode`. |
| 3 | Tại prompt của Gemini CLI, gõ slash command: `/auth signin`. |
| 4 | Chọn **Use Gemini API Key**, dán key vừa tạo. Sau khi xong, có thể thoát CLI (`/exit` hoặc `Ctrl+C`) nếu chỉ cấu hình một lần. |
| 5 (tùy chọn, khuyến nghị cho lab có pipe) | Trong **cùng** terminal sẽ chạy lệnh `| gemini`, đặt biến môi trường: `export GEMINI_API_KEY="key-của-bạn"` (Windows PowerShell: `$env:GEMINI_API_KEY="..."`). Một số phiên bản Gemini CLI yêu cầu biến này khi chạy ở chế độ headless (`-p`). |

```bash
gemini --approval-mode yolo
```

```text
/auth signin
```

---

## 6. Bài thực hành (Labs)

Các lệnh mẫu dùng toán tử **pipe** (`|`) để đưa JSON từ `gws` vào `gemini`.

**Lưu ý:** Với luồng có pipe, Gemini CLI phải chạy **headless**: dùng cờ **`-p`** (hoặc `--prompt`) kèm nội dung prompt. Prompt chỉ viết sau vị trí `gemini` (không có `-p`) sẽ mở chế độ tương tác và **không** phù hợp khi kết hợp `|`. Hoàn tất mục 5 và (nếu CLI báo thiếu key) đặt `GEMINI_API_KEY` như bảng ở trên.

### Lab 1 — Phân tích danh sách file Drive

**Kịch bản:** Lấy danh sách file từ Drive và nhờ mô hình gợi ý giữ / xóa dựa trên tên và metadata trong JSON.

```bash
gws drive files list --params '{"pageSize": 20}' --format json | gemini -p "Đây là danh sách file trên Google Drive của tôi định dạng JSON. Hãy phân tích và trả về cho tôi một bảng Markdown gồm: Tên file, Kích thước, và Gợi ý của bạn (Nên giữ hay Xóa đi dựa vào tên file xem nó có vẻ quan trọng hay là rác)."
```

### Lab 2 — Lịch hôm nay + nội dung sáng tạo

**Kịch bản:** Đọc agenda Calendar hôm nay và tạo thơ / câu động viên theo từng sự kiện.

```bash
gws calendar +agenda --today --format json | gemini -p "Đây là lịch họp hôm nay của tôi. Hãy liệt kê các buổi họp ra, và với mỗi buổi họp, hãy sáng tác 1 câu thơ vui nhộn (2 câu lục bát) hoặc 1 câu an ủi chữa lành phù hợp với tên buổi họp để động viên tôi đi họp."
```

### Lab 3 — Tóm tắt email khuyến mãi

**Kịch bản:** Lấy vài thư mục Promotions và trích thương hiệu, mức giảm, mã (nếu có).

```bash
gws gmail +triage --max 5 --query "category:promotions" --format json | gemini -p "Đọc các email quảng cáo này. Chỉ lọc ra cho tôi tên Thương hiệu, Mức giảm giá, và Mã Code (nếu có). Format dưới dạng bảng rõ ràng."
```

### Lab 4 — Soạn email CSKH rồi gửi qua Gmail

**Bước 1** — Tạo nội dung bằng Gemini, ghi ra file:

```bash
echo "Khách hàng: Anh Tú, Sản phẩm: Bàn phím cơ. Khách hàng này phàn nàn là giao hàng hơi chậm" | gemini -p "Đóng vai nhân viên CSKH, viết 1 email ngắn gọn xin lỗi vụ giao chậm, cảm ơn vì đã mua bàn phím cơ và tặng mã giảm GIA50. Chỉ in ra nội dung email, không in gì thêm." > email_body.txt
```

**Bước 2** — Gửi mail (thay email người nhận và tiêu đề cho đúng kịch bản của bạn):

```bash
gws gmail +send --to "tu.nguyen@example.com" --subject "Thư xin lỗi và Cảm ơn từ Shop GDG" --body "$(cat email_body.txt)"
```

Để **kiểm tra lệnh gửi** mà không gửi thật, thêm `--dry-run` (in ra payload JSON có `dry_run: true`).

---

## 7. Nộp bài

BTC cung cấp một Google Sheet công khai. Sau khi hoàn thành codelab, ghi thông tin của bạn bằng CLI.

**Nội dung cần gửi:** Họ tên, cảm nhận về codelab, và (nếu có) kết quả thơ / quote từ Lab 2.

Thay `SPREADSHEET_ID_FROM_MC` bằng ID do MC công bố:

```bash
gws sheets +append --spreadsheet "SPREADSHEET_ID_FROM_MC" --values "Tên của bạn, Cảm nhận ngắn, Nội dung từ Lab 2"
```

---

*Tài liệu dùng cho GDG Cloud Đà Nẵng — Build with AI.*
