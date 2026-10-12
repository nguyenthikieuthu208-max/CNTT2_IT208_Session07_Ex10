*(Bài tập tổng hợp) Terminal Nâng Cao & Tiền Đề Scripting* 

Phần 1 – Đọc và định vị tệp tin

**Câu 1: Lệnh in toàn bộ nội dung file**

powershell  
Get-Content \-Path "logs\\server.log"

**Câu 2: Phân tích cơ chế sử dụng RAM**

* **Notepad (Treo máy):** Hoạt động theo cơ chế **In-Memory** (tải toàn bộ dữ liệu 500MB cùng một lúc vào RAM) rồi mới dựng giao diện đồ họa. Hành động này tiêu tốn đột ngột một lượng lớn tài nguyên bộ nhớ, làm nghẽn mạch CPU/RAM dẫn đến treo phần mềm (Not Responding).  
* **Terminal PowerShell (Mượt mà):** Sử dụng cơ chế đọc file **theo luồng (Stream/Pipeline)**. Nó chỉ nạp một lượng nhỏ dữ liệu thô vào RAM tại một thời điểm, xử lý và đẩy ra luồng xuất chuẩn (stdout) theo kiểu tuần tự nên không làm tràn bộ nhớ.

**Câu 3: Lệnh tìm đệ quy tất cả file .log**

powershell  
Get-ChildItem \-Path "." \-Recurse \-Filter "\*.log"

Phần 2 – Lọc dữ liệu và Pipe

**Câu 4: Lệnh lọc dòng chứa chữ ERROR**

powershell  
Select-String \-Path "logs\\server.log" \-Pattern "ERROR"

**Câu 5: Phân biệt bản chất và hoàn thành bảng**

* Bản chất: Get-ChildItem dùng để tìm tên và vị trí file trên ổ đĩa (Metadata). Select-String dùng để đọc và chắt lọc nội dung chuỗi văn bản nằm phía trong file (Content).

| Câu hỏi | Lệnh PowerShell |
| ----- | ----- |
| *"File server.log đang nằm ở thư mục nào?"* | Get-ChildItem \-Path "." \-Recurse \-Filter "server.log" |
| *"Bên trong file này có dòng nào chứa lỗi 404 không?"* | Select-String \-Path "logs\\server.log" \-Pattern "404" |

**Câu 6: Lệnh kết hợp Pipe |**

**Đọc và lọc chữ 404:**  
powershell  
Get-Content \-Path "logs\\server.log" | Select-String \-Pattern "404"  
**Tìm tất cả file .log và lọc dòng không chứa INFO:**  
powershell  
Get-ChildItem \-Path "." \-Recurse \-Filter "\*.log" | Select-String \-Pattern "INFO" \-NotMatch

Phần 3 – Toán tử chuyển hướng \> và \>\>

**Câu 7: Lệnh trích xuất và ghi đè tạo báo cáo**

powershell  
Get-Content \-Path "logs\\server.log" | Select-String \-Pattern "404" \> reports\\daily\_report.txt

**Câu 8: Lệnh ghi nối thêm lỗi 404 mới và giải thích rủi ro**

**Lệnh ghi nối tiếp:**  
powershell  
"10:42:15 ERROR 404 Not Found /api/products" \>\> reports\\daily\_report.txt

* Hãy thận trọng khi sử dụng mã.  
* **Giải thích rủi ro:** Nếu lỡ tay dùng nhầm toán tử ghi đè (\>), hệ thống sẽ lập tức **xóa sạch toàn bộ nội dung lịch sử báo cáo cũ** đã tạo ở Câu 7 và đưa dung lượng file về 0 trước khi ghi dòng lỗi mới vào, gây mất mát dữ liệu.

Phần 4 – Shell Script và lệnh echo

**Câu 9: Lệnh xuất thông báo tiến trình và tạo dòng tiêu đề**

powershell  
Write-Output "Dang loc log..."  
"=== LOG REPORT \===" \> reports\\daily\_report.txt

**Câu 10: Viết nội dung file kịch bản tự động scripts\\auto\_report.ps1**

powershell  
\# Script tu dong hoa trich xuat bao cao loi 404 hang ngay cho DevOps Lab  
Write-Output "=== BAT DAU TIEN TRINH TU DONG HOA \==="

\# Buoc 1: Khoi tao file bao cao voi dong tieu de moi  
"=== LOG REPORT \===" \> reports\\daily\_report.txt

\# Buoc 2: Doc file, loc loi 404 va ghi noi tiep vao file bao cao  
Get-Content \-Path "logs\\server.log" | Select-String \-Pattern "404" \>\> reports\\daily\_report.txt

Write-Output "=== DA HOAN THANH XUAT BAO CAO \==="

Phần 5 – Viết, chạy và thẩm định Script AI

**Câu 11: Quy trình 3 bước thực thi Script**

**(a) Lệnh thực thi file script trong PowerShell:**  
powershell  
.\\scripts\\auto\_report.ps1

**(b) Lệnh kiểm tra nội dung file báo cáo kết quả:**  
powershell  
Get-Content \-Path "reports\\daily\_report.txt"

**Câu 12: Áp dụng 3 Nguyên tắc Vàng thẩm định Script AI**

* **(a) Dòng lệnh nguy hiểm:** Remove-Item \-Recurse \-Force C:\\\*  
  * *Hậu quả:* Dòng này ra lệnh xóa đệ quy (\-Recurse) và ép buộc (\-Force) toàn bộ mọi dữ liệu bắt đầu từ gốc ổ đĩa hệ thống C:\\. Nếu chạy với quyền quản trị viên, nó sẽ **quét sạch hệ điều hành Windows**, làm sập toàn bộ máy tính và gây mất dữ liệu vĩnh viễn \[A\].  
* **(b) Sửa lại đường dẫn an toàn cục bộ trong project:**  
  powershell  
  Remove-Item \-Path ".\\temp\\\*" \-Recurse \-Force

