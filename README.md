# 🚀 Codelab GDG Cloud: "Hack" Thời Gian Với Gemini CLI & Google Workspace CLI

Chào mừng các pháp sư code đến với Codelab Build with AI! Hôm nay, chúng ta sẽ kết hợp "não bộ" của **Gemini CLI** và "chân tay" của **Google Workspace CLI (`gws`)** để tự động hóa những công việc nhàm chán nhất.

**Mục tiêu:** Thoát kiếp làm "chạy vặt" chốn công sở, để AI và CLI làm thay bạn! 🤖✨

## 🛠️ PHẦN 1: CHUẨN BỊ "ĐỒ NGHỀ" (Prerequisites)

*(Dành cho máy tính trắng tinh, chưa có gì)*

### 1. Cài đặt Node.js (Bắt buộc)

Cả `geminicli` và `gws` đều chạy trên nền tảng Node.js.

* **Mac/Windows/Linux:** Tải và cài đặt bản LTS mới nhất tại nodejs.org.
* Mở Terminal (Mac) hoặc PowerShell/CMD (Windows), gõ thử: `node -v` và `npm -v` thấy ra số phiên bản là thành công.

### 2. Cài đặt Google Cloud CLI (`gcloud`)

Dùng để giao tiếp với Google Cloud Platform (GCP).

* Làm theo hướng dẫn cài đặt trên trang chủ Google cho Windows hoặc Mac.
* Cài xong gõ: `gcloud --version`.

### 3. Cài đặt Gemini CLI & Google Workspace CLI

Chạy lệnh sau trong Terminal/CMD:

```bash
npm install -g @google/gemini-cli
npm install -g @googleworkspace/cli

```

## ☁️ PHẦN 2: KHAI BÁO VỚI "VŨ TRỤ" GOOGLE (Setup & Auth)

### Bước 1: Lấy API Key cho Gemini (Não bộ)

1. Vào trang Google AI Studio (đăng nhập bằng tài khoản Google).
2. Bấm **"Create API Key"**.
3. Copy đoạn mã đó. Mở Terminal gõ lệnh:
```bash
gemini auth --key "DÁN_API_KEY_CỦA_BẠN_VÀO_ĐÂY"

```


*Vậy là não bộ Gemini đã sẵn sàng!*

### Bước 2: Khởi tạo Google Workspace CLI Tự Động (Chân tay)

`gws` cung cấp sẵn một công cụ setup tự động tuyệt vời, giúp chúng ta rút ngắn quy trình trên Cloud Console.

1. Trước tiên, đảm bảo bạn đã đăng nhập Google Cloud CLI:
```bash
gcloud auth login

```


*(Trình duyệt hiện ra, bạn đăng nhập tài khoản Google của mình)*
2. Chạy trình thiết lập tự động của gws:
```bash
gws auth setup

```


3. **Làm theo hướng dẫn tương tác trên Terminal (Sử dụng phím mũi tên và Enter):**
* **Step 1 & 2:** Tool tự nhận diện `gcloud` và email của bạn.
* **Step 3 (GCP project):** Chọn `Create new project` (Tạo mới) -> Đặt tên vd: `gdg-cloud-codelab-tenban`.
* **Step 4 (Workspace APIs):** Tool tự động đánh dấu các API cần thiết (có thể dùng phím Space để chọn thêm Docs, Slides, Forms, Chat nếu muốn) -> Bấm `Enter` để bật (enable). Hãy kiên nhẫn đợi màn hình load.
* **Step 5 (OAuth credentials):** Nếu Terminal báo xanh `Setup complete! Starting 'gws auth login'...`, bạn chuyển sang Bước 2.5 ngay lập tức. *(Lưu ý: Không tiếp tục đăng nhập vội ở bước này).*



### Bước 2.5: Thêm Test User (Cực kỳ quan trọng - Chống lỗi 403) 🚨

Vì ứng dụng của bạn đang ở chế độ Thử nghiệm (Testing), Google sẽ CHẶN MỌI TRUY CẬP nếu bạn không khai báo email được phép test.

1. Vào trang Google Cloud Console -> **Google Auth Platform** -> Chọn tab **Audience** (Đảm bảo đang chọn đúng Project vừa tạo ở trên).
2. Kéo xuống tìm phần **Test users** (Người dùng thử nghiệm).
3. Bấm nút **+ ADD USERS** (Thêm người dùng).
4. **Gõ chính xác địa chỉ Gmail của bạn** vào ô trống -> Bấm biểu tượng Lưu/Add. Đảm bảo email của bạn đã hiện trong danh sách bên dưới.

### Bước 3: Đăng nhập và Cấp quyền cho Workspace CLI

Sau khi đã nạp email vào Test users, bước cuối cùng là cho phép công cụ này truy cập dữ liệu. Mở Terminal, gõ lệnh sau:

```bash
gws auth login

```

Trình duyệt sẽ tự động mở ra. Bạn thực hiện đúng các bước sau:

1. Chọn tài khoản Google của bạn.
2. Màn hình hiện cảnh báo đen **"Google hasn't verified this app"** (Google chưa xác minh ứng dụng này). Đừng lo vì đây là app nội bộ của bạn -> Bấm nút **Continue (Tiếp tục)**.
3. Ở màn hình cấp quyền tiếp theo, hãy chú ý **tích chọn vào ô "Select all"** (hoặc tích tay vào tất cả các quyền hiển thị như Gmail, Drive, Calendar...) -> Cuộn xuống và bấm **Continue**.
4. Quay lại Terminal, nếu bạn thấy đoạn JSON có chứa `"status": "success"`, xin chúc mừng! Bạn đã hoàn tất cấu hình.

### Bước 4: Test thử "Siêu quyền lực" 🚀

Hãy gõ thử lệnh sau vào Terminal để xem chúng ta đã liên kết thành công với Google Drive chưa:

```bash
gws drive files list --params '{"pageSize": 1}'

```

*(Nếu Terminal trả về một đoạn JSON chứa danh sách các file trong Drive của bạn như "Bảng tính không có tiêu đề", "Tài liệu không có tiêu đề"... thì hệ thống đã sẵn sàng 100%!).*

---

## ⚔️ PHẦN 3: THỰC CHIẾN (THE LABS)

*Cú pháp thần thánh của ngày hôm nay là Dấu Pipe `|` (Đẩy kết quả của lệnh này làm đầu vào cho lệnh kia).*

### 🧹 Lab 1: Thợ Săn Rác Drive (Dọn dẹp "Mây" cuối tuần)

*Kịch bản: Drive đầy, bạn cần AI phân tích xem file nào cũ, to mà không dùng để dọn.*

**Lệnh thực thi:**

```bash
gws drive files list --params '{"pageSize": 20}' --format json | gemini "Đây là danh sách file trên Google Drive của tôi định dạng JSON. Hãy phân tích và trả về cho tôi một bảng Markdown gồm: Tên file, Kích thước, và Gợi ý của bạn (Nên giữ hay Xóa đi dựa vào tên file xem nó có vẻ quan trọng hay là rác)."

```

👉 *Kết quả:* Gemini sẽ "ngửi" tên file (như `untitled.doc` hay `backup_2010.zip`) và khuyên bạn nên xóa cái nào.

### 🧘‍♂️ Lab 2: Trợ lý Nhắc Lịch Họp kiểu "Chữa Lành"

*Kịch bản: Check lịch hôm nay và nhờ AI làm một bài thơ hoặc câu quote động viên cho từng buổi họp để có mood làm việc.*

**Lệnh thực thi:**

```bash
gws calendar +agenda --today --format json | gemini "Đây là lịch họp hôm nay của tôi. Hãy liệt kê các buổi họp ra, và với mỗi buổi họp, hãy sáng tác 1 câu thơ vui nhộn (2 câu lục bát) hoặc 1 câu an ủi chữa lành phù hợp với tên buổi họp để động viên tôi đi họp."

```

👉 *Kết quả:* "10h - Họp với sếp: Sếp la thì kệ sếp la / Mỉm cười cái nhẹ cho qua tháng ngày".

### 🛍️ Lab 3: Máy Lọc Email "Săn Deal Sập Sàn"

*Kịch bản: Cuối tuần muốn tiêu tiền, nhờ AI lùng sục trong Gmail các email quảng cáo và tóm tắt lại deal nào ngon nhất.*

**Lệnh thực thi:**

```bash
gws gmail +triage --max 5 --query "category:promotions" --format json | gemini "Đọc các email quảng cáo này. Chỉ lọc ra cho tôi tên Thương hiệu, Mức giảm giá, và Mã Code (nếu có). Format dưới dạng bảng rõ ràng."

```

### 💌 Lab 4: Trợ Lý Chăm Sóc Khách Hàng (Đỉnh Cao)

*Kịch bản: Bạn có một danh sách tên khách hàng và sản phẩm họ mua. Nhờ AI tự động viết email cảm ơn ĐỘC QUYỀN cho từng người và gửi đi trực tiếp.*

**Bước 1:** Dùng AI tạo nội dung từ 1 thông tin mẫu.

```bash
echo "Khách hàng: Anh Tú, Sản phẩm: Bàn phím cơ. Khách hàng này phàn nàn là giao hàng hơi chậm" | gemini "Đóng vai nhân viên CSKH, viết 1 email ngắn gọn xin lỗi vụ giao chậm, cảm ơn vì đã mua bàn phím cơ và tặng mã giảm GIA50. Chỉ in ra nội dung email, không in gì thêm." > email_body.txt

```

**Bước 2:** Bắn nội dung đó thẳng vào Gmail.

```bash
gws gmail +send --to "tu.nguyen@example.com" --subject "Thư xin lỗi và Cảm ơn từ Shop GDG" --body "$(cat email_body.txt)"

```

---

## 🎁 PHẦN 4: NỘP BÀI NHẬN QUÀ (Submission)

Ban tổ chức đã tạo sẵn một file Google Sheets công khai. Để chứng minh bạn đã làm xong Codelab và nhận Swag, hãy dùng CLI bắn thẳng thông tin của bạn vào Sheet đó!

**Yêu cầu:** Ghi "Tên bạn, Cảm nhận về Codelab, Kết quả bài thơ chữa lành ở Lab 2".

**Lệnh nộp bài:**
*(Thay `ID_CỦA_SHEET_BTC` bằng ID mà MC cung cấp trên màn hình)*

```bash
gws sheets +append --spreadsheet "ID_CỦA_SHEET_BTC" --values "Tên của bạn, Quá đỉnh!, Nội dung bài thơ ở đây"

```

**Hoàn thành! Hãy lên bàn check-in để nhận phần thưởng độc quyền từ Google Developer Groups! 🎉**
