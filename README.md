# BT2 - QUẢN TRỊ CSDL

## Phần mở đầu

### Thông tin cá nhân
- Họ tên: Trần Đình Quang
- Lớp: K59KMT
- MSSV: K235480106057
- Chủ đề: Quản lý thư viện

---

# Phần 1: Thiết kế và Khởi tạo Cấu trúc Dữ liệu

- Tên database: QuanLyThuVien_K235480106057

<img width="1918" height="1077" alt="image" src="https://github.com/user-attachments/assets/ccdf7499-521b-443b-9220-829ae43a8a52" />

## Mô tả logic

Hệ thống quản lý thư viện gồm 3 bảng chính:

- Bảng SinhVien:  
Lưu thông tin sinh viên gồm MaSV (khóa chính), TenSV, Lop.

- Bảng Sach:  
Lưu thông tin sách gồm MaSach (khóa chính), TenSach, SoLuong, GiaTien.  
Trong đó SoLuong có ràng buộc CHECK >= 0.

- Bảng PhieuMuon:  
Lưu thông tin mượn sách gồm MaPM (khóa chính), MaSV, MaSach, NgayMuon, NgayTra.

## Logic hệ thống

- Một sinh viên có thể mượn nhiều sách → quan hệ 1-n giữa SinhVien và PhieuMuon  
- Một sách có thể được mượn nhiều lần → quan hệ 1-n giữa Sach và PhieuMuon  
- PhieuMuon là bảng liên kết giữa SinhVien và Sach  

## Ràng buộc dữ liệu

- PK (khóa chính): MaSV, MaSach, MaPM  
- FK (khóa ngoại):  
  + MaSV → SinhVien  
  + MaSach → Sach  
- CHECK: SoLuong >= 0  

---

## Code SQL
```sql
CREATE DATABASE QuanLyThuVien_K235480106057;
GO
USE QuanLyThuVien_K235480106057;

CREATE TABLE [SinhVien] (
    [MaSV] INT PRIMARY KEY,
    [TenSV] NVARCHAR(50),
    [Lop] NVARCHAR(20)
);

CREATE TABLE [Sach] (
    [MaSach] INT PRIMARY KEY,
    [TenSach] NVARCHAR(100),
    [SoLuong] INT CHECK ([SoLuong] >= 0),
    [GiaTien] MONEY
);

CREATE TABLE [PhieuMuon] (
    [MaPM] INT PRIMARY KEY,
    [MaSV] INT,
    [MaSach] INT,
    [NgayMuon] DATE,
    [NgayTra] DATE,
    FOREIGN KEY ([MaSV]) REFERENCES [SinhVien]([MaSV]),
    FOREIGN KEY ([MaSach]) REFERENCES [Sach]([MaSach])
);
```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/3d8fda6f-5ea1-4667-a30f-31ec4e47df53" />

---
# Phần 2: Truy vấn dữ liệu

## Mô tả
Phần này thực hiện các truy vấn dữ liệu trong hệ thống quản lý thư viện, bao gồm:
- Truy vấn cơ bản (SELECT)
- Truy vấn có điều kiện (WHERE)
- Truy vấn kết hợp nhiều bảng (JOIN)

Các truy vấn giúp khai thác dữ liệu từ các bảng đã tạo, phục vụ cho việc quản lý và theo dõi thông tin sinh viên, sách và phiếu mượn.

---

## Truy vấn cơ bản
```sql
SELECT * FROM SinhVien;
SELECT * FROM Sach;
SELECT * FROM PhieuMuon;
```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/3213ca58-8f63-4163-9a55-f8d4c3ca8234" />
*Ảnh hiển thị kết quả truy vấn dữ liệu từ 3 bảng SinhVien, Sach và PhieuMuon. 
Dữ liệu đã được thêm thành công và phục vụ cho các truy vấn và xử lý ở các phần tiếp theo.

## Truy vấn có điều kiện
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/80085f10-a0c5-4228-9cb7-904428ef7e59" />
*Lọc ra các sách có số lượng lớn hơn 5.

## Truy vấn kết hợp bảng (JOIN)
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/639c5973-cb70-4c8e-9b27-c289f35ea697" />
*Kết hợp dữ liệu từ 3 bảng để hiển thị thông tin sinh viên mượn sách và ngày mượn.

---
# Phần 3: Xây dựng Function

## 1. Hàm Built-in trong SQL Server

SQL Server cung cấp nhiều hàm có sẵn như:
- Hàm chuỗi: LEN(), UPPER()
- Hàm ngày: GETDATE()
- Hàm hệ thống: DB_NAME()

### Ví dụ sử dụng
```sql
SELECT LEN(N'QuanTriCSDL') AS DoDai;
SELECT GETDATE() AS ThoiGian;
SELECT DB_NAME() AS TenDB;
```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/02e502d9-9df4-45f9-afcd-1fca2fd3a00f" />
*Sử dụng một số hàm built-in trong SQL Server.

## 2. Hàm do người dùng tự định nghĩa

Hàm do người dùng tạo nhằm xử lý các bài toán riêng mà hàm có sẵn không đáp ứng.

Các loại function:
- Scalar Function: trả về 1 giá trị
- Inline Table Function: trả về bảng đơn giản
- Multi-statement Function: xử lý logic phức tạp

Mục đích:
- Tái sử dụng code
- Tối ưu xử lý
- Phù hợp bài toán riêng

## 3. Scalar Function

### Ý tưởng
Tính số ngày mượn sách.

### Code
```sql
CREATE FUNCTION fn_TinhSoNgayMuon
(
    @NgayMuon DATE,
    @NgayTra DATE
)
RETURNS INT
AS
BEGIN
    RETURN DATEDIFF(DAY, @NgayMuon, @NgayTra);
END;
```
### TẠO HÀM

<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/31b68fdb-bb43-46ca-96ae-677e0aff8d0d" />

### GỌI HÀM

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/ca7348f8-499b-467d-a686-69290305f3de" />


*Tính số ngày mượn bằng hàm tự tạo.

## 4. Inline Table-Valued Function

### Ý tưởng
Lấy danh sách sách còn trong kho.

### Code
```sql
CREATE FUNCTION fn_SachCon()
RETURNS TABLE
AS
RETURN
(
    SELECT * FROM Sach WHERE SoLuong > 0
);
```
### TẠO HÀM

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/9c72da24-c031-465c-a375-9f7d3452eae5" />

### GỌI HÀM

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/373f2d8f-1255-4348-bbba-0f817d9a6baa" />
*Thống kê số lần mượn của sinh viên.

## 5. Multi-statement Table-Valued Function

### Ý tưởng
Thống kê số lần mượn sách của từng sinh viên.

### Code
```sql
CREATE FUNCTION fn_ThongKeMuon()
RETURNS @Result TABLE
(
    MaSV INT,
    SoLanMuon INT
)
AS
BEGIN
    INSERT INTO @Result
    SELECT MaSV, COUNT(*)
    FROM PhieuMuon
    GROUP BY MaSV;
    RETURN;
END;
```
### TẠO HÀM

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/a77b39de-9949-4033-957c-41cccbe7ce4f" />

### GỌI HÀM

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/76175756-b5d8-40a8-bbbf-983a014c190a" />
*Thống kê số lần mượn sách của sinh viên.

