# Hướng dẫn thiết lập Google Apps Script cho Form Tuyển Thành Viên

## Bước 1: Tạo Google Sheet mới

1. Truy cập [Google Sheets](https://sheets.google.com)
2. Tạo Sheet mới với tên "Đăng ký CTES K17"
3. Tạo header row với các cột sau (từ A1 đến T1):

| A         | B         | C         | D    | E         | F           | G     | H             | I        | J               | K         | L         | M        | N        | O       | P    | Q              | R           | S         | T         |
| --------- | --------- | --------- | ---- | --------- | ----------- | ----- | ------------- | -------- | --------------- | --------- | --------- | -------- | -------- | ------- | ---- | -------------- | ----------- | --------- | --------- |
| Thời gian | Họ và Tên | Ngày sinh | MSSV | Giới tính | Trường/Viện | Email | Số điện thoại | Facebook | Nguồn thông tin | Tính cách | Điểm mạnh | Điểm yếu | Sở thích | Kỹ năng | Mảng | Điểm mạnh mảng | Kinh nghiệm | Portfolio | Mong muốn |

## Bước 2: Tạo Google Apps Script

1. Trong Google Sheet, click **Extensions** > **Apps Script**
2. Xóa code mặc định và dán code sau:

```javascript
function doPost(e) {
    try {
        // Log để debug
        console.log("Received data:", e.postData.contents);

        // Parse JSON data
        const data = JSON.parse(e.postData.contents);

        // Mở Google Sheet (thay YOUR_SHEET_ID bằng ID thực tế)
        const sheet = SpreadsheetApp.openById("YOUR_SHEET_ID").getActiveSheet();

        // Tạo array dữ liệu theo thứ tự cột
        const rowData = [
            data.timestamp || new Date().toLocaleString("vi-VN"),
            data.fullName || "",
            data.dateOfBirth || "",
            data.studentId || "",
            data.gender || "",
            data.school || "",
            data.email || "",
            data.phone || "",
            data.facebook || "",
            data.source || "",
            data.personality || "",
            data.strengths || "",
            data.weaknesses || "",
            data.hobbies || "",
            data.skills || "",
            data.department || "",
            data.departmentStrengths || "",
            data.relevantExperience || "",
            data.portfolio || "",
            data.expectations || "",
        ];

        // Thêm dữ liệu vào sheet
        sheet.appendRow(rowData);

        // Trả về response thành công
        return ContentService.createTextOutput(
            JSON.stringify({
                status: "success",
                message: "Đăng ký thành công!",
            })
        ).setMimeType(ContentService.MimeType.JSON);
    } catch (error) {
        console.error("Error:", error);

        // Trả về response lỗi
        return ContentService.createTextOutput(
            JSON.stringify({
                status: "error",
                message: "Có lỗi xảy ra: " + error.toString(),
            })
        ).setMimeType(ContentService.MimeType.JSON);
    }
}

// Test function (optional)
function testDoPost() {
    const testData = {
        postData: {
            contents: JSON.stringify({
                fullName: "Nguyễn Văn A",
                email: "test@example.com",
                phone: "0123456789",
                timestamp: new Date().toLocaleString("vi-VN"),
            }),
        },
    };

    const result = doPost(testData);
    console.log(result.getContent());
}
```

## Bước 3: Cấu hình và Deploy

1. **Thay YOUR_SHEET_ID**:

    - Copy ID từ URL của Google Sheet (phần giữa `/d/` và `/edit`)
    - Ví dụ: `https://docs.google.com/spreadsheets/d/1ABC...XYZ/edit` → ID là `1ABC...XYZ`

2. **Deploy Web App**:

    - Click **Deploy** > **New deployment**
    - Chọn type: **Web app**
    - Description: "CTES Registration Form Handler"
    - Execute as: **Me**
    - Who has access: **Anyone**
    - Click **Deploy**

3. **Copy Web App URL**:
    - Sau khi deploy, copy URL dạng: `https://script.google.com/macros/s/AKfyc.../exec`

## Bước 4: Cập nhật Form HTML

Thay thế URL trong file `index.html` tại dòng:

```javascript
"https://script.google.com/macros/s/AKfycbwUtt.../exec";
```

## Bước 5: Test và Verify

1. Test form trên website
2. Kiểm tra dữ liệu trong Google Sheet
3. Xem logs trong Apps Script để debug nếu cần

## Lưu ý quan trọng:

-   **CORS**: Google Apps Script tự động xử lý CORS
-   **Permissions**: Apps Script sẽ yêu cầu quyền truy cập Google Sheets
-   **Rate Limiting**: Apps Script có giới hạn 20,000 requests/day
-   **Data Validation**: Code đã bao gồm validation cơ bản

## Cấu trúc dữ liệu nhận được:

```json
{
    "fullName": "Họ và tên",
    "dateOfBirth": "YYYY-MM-DD",
    "studentId": "Mã số sinh viên",
    "gender": "Nam/Nữ/Khác",
    "school": "Tên trường/viện",
    "email": "email@example.com",
    "phone": "0123456789",
    "facebook": "https://facebook.com/profile",
    "source": "Nguồn biết thông tin",
    "personality": "3 từ mô tả tính cách",
    "strengths": "Điểm mạnh",
    "weaknesses": "Điểm yếu",
    "hobbies": "Sở thích",
    "skills": "Kỹ năng 1, Kỹ năng 2",
    "department": "Mảng đăng ký",
    "departmentStrengths": "Điểm mạnh phù hợp mảng",
    "relevantExperience": "Kinh nghiệm liên quan",
    "portfolio": "Link portfolio",
    "expectations": "Mong muốn khi tham gia",
    "timestamp": "Thời gian đăng ký"
}
```

## Troubleshooting:

-   Nếu không nhận được dữ liệu: Kiểm tra permissions và Sheet ID
-   Nếu có lỗi CORS: Đảm bảo đã deploy với "Anyone" access
-   Nếu dữ liệu sai format: Kiểm tra thứ tự cột trong Sheet

Chúc bạn thiết lập thành công! 🎉
