# Quản lý Sinh viên – ADO.NET (mô hình 3 tầng)

Solution **QuanLySinhVien** được tạo mới hoàn toàn (làm riêng, không dùng lại project có sẵn trên máy), gồm 3 project:

| Project | Vai trò | Nội dung chính |
|---|---|---|
| `QuanLySinhVien.Data` | Tầng truy xuất dữ liệu (DAL) | `DataHelper`, **`SinhVienDAO`**, **`LopHocDAO`**, entity `SinhVien` / `LopHoc` |
| `QuanLySinhVien.Business` | Tầng nghiệp vụ (BUS) | `SinhVienBUS`, `LopHocBUS` – kiểm tra dữ liệu, quy tắc nghiệp vụ |
| `QuanLySinhVien.View` | Tầng giao diện (WinForms) | `FrmMain`, `FrmSinhVien`, `FrmLopHoc` |

## Quy tắc xử lý ngoại lệ (theo yêu cầu đề bài)

- **Tầng DAL và tầng BUS**: mọi phương thức public đều bọc `try/catch` và **ném ngoại lệ ra ngoài** (kèm thông điệp tiếng Việt và `InnerException` để truy vết).
- **Tầng View**: mọi sự kiện gọi xuống tầng Business đều bọc `try/catch`; ngoại lệ được hiển thị bằng `LoiHelper.HienThiLoi` (in cả chuỗi ngoại lệ lồng nhau).
- Luồng lan truyền: `SQL Server` → `DAL (ném)` → `Business (ném)` → `View (bắt và hiển thị)`.

## Cách chạy

1. Mở `Database/QLSV.sql` bằng SQL Server Management Studio và **Execute** để tạo CSDL `QLSV` + dữ liệu mẫu.
   (Lưu ý: script sẽ XÓA và TẠO LẠI 2 bảng `SinhVien`, `LopHoc` trong CSDL `QLSV`.)
2. Mở `QuanLySinhVien.sln` bằng Visual Studio 2019/2022.
3. Kiểm tra chuỗi kết nối trong `QuanLySinhVien.View\App.config` (mục `connectionStrings`, tên `QLSV`) – sửa `Server=...` cho đúng máy chủ SQL Server của bạn.
   (Nếu không có App.config, `DataHelper` dùng chuỗi mặc định `Server=LAPTOP-6SN65NCS\HUECTMDB;Database=QLSV;...` giống mã nguồn gốc.)
4. Chọn `QuanLySinhVien.View` làm Startup Project và nhấn **F5**.

## Điểm đã sửa / cải tiến so với mã nguồn cũ

- Toàn bộ câu lệnh SQL dùng **tham số hóa** (`SqlParameter`) → chống SQL Injection, không lỗi khi dữ liệu chứa dấu nháy.
- Sửa lỗi câu `UPDATE` sai cú pháp (đúng thứ tự `UPDATE ... SET ... WHERE`, đủ dấu nháy).
- `DeleteSinhVien` giờ **xóa thật trong CSDL** (bản cũ chỉ xóa phần tử trong `List` rỗng nên không có tác dụng).
- `GetSinhVienByMaSV` truy vấn CSDL thay vì tìm trong `List` chưa được nạp.
- Kiểm tra `DBNull` khi đọc `Diem`, `NgaySinh`… tránh lỗi khi dữ liệu NULL.
- Minh họa đầy đủ các đối tượng ADO.NET: `SqlConnection`, `SqlCommand`, `SqlParameter`, `SqlDataReader`, `SqlDataAdapter` + `DataTable`, `ExecuteScalar`.
- Tách đúng 3 tầng: **View ⇄ Business ⇄ Data**; View không gọi trực tiếp lớp DAO (chỉ dùng entity `SinhVien`, `LopHoc` để hiển thị).
- Tầng BUS kiểm tra nghiệp vụ: mã trùng, lớp không tồn tại, điểm ngoài 0–10, email sai định dạng, ngày sinh sai định dạng, không cho xóa lớp còn sinh viên…

## Cấu trúc thư mục

```
QuanLySinhVien.sln
Database/QLSV.sql                     ← script tạo CSDL + dữ liệu mẫu
QuanLySinhVien.Data/
    Entity/SinhVien.cs, LopHoc.cs
    DAL/DataHelper.cs, SinhVienDAO.cs, LopHocDAO.cs
QuanLySinhVien.Business/
    SinhVienBUS.cs, LopHocBUS.cs
QuanLySinhVien.View/
    Forms/FrmMain, FrmSinhVien, FrmLopHoc
    Utilities/LoiHelper.cs
    App.config                          ← chuỗi kết nối
```
