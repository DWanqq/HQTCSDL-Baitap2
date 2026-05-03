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
*Chú thích: Tạo thành công database QuanLyThuVien và chuyển sang sử dụng để thực hiện các thao tác tiếp theo.*

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
*Chú thích: Đã tạo thành công 3 bảng SinhVien, Sach, PhieuMuon cùng các ràng buộc khóa chính, khóa ngoại và CHECK.*

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
*Ảnh hiển thị kết quả truy vấn dữ liệu từ 3 bảng SinhVien, Sach và PhieuMuon.*
*Chú thích:Dữ liệu đã được thêm thành công và phục vụ cho các truy vấn và xử lý ở các phần tiếp theo.*

## Truy vấn có điều kiện
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/80085f10-a0c5-4228-9cb7-904428ef7e59" />
*Chú thích:Lọc ra các sách có số lượng lớn hơn 5.*

## Truy vấn kết hợp bảng (JOIN)
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/639c5973-cb70-4c8e-9b27-c289f35ea697" />
*Chú thích:Kết hợp dữ liệu từ 3 bảng để hiển thị thông tin sinh viên mượn sách và ngày mượn.*

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
*Chú thích:Sử dụng một số hàm built-in trong SQL Server.*

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
### Mô tả chức năng
Hàm tính số ngày mượn sách dựa trên ngày mượn và ngày trả.
### Tạo hàm 

<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/31b68fdb-bb43-46ca-96ae-677e0aff8d0d" />

### Gọi hàm

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/ca7348f8-499b-467d-a686-69290305f3de" />
*Chú thích:Tính số ngày mượn bằng hàm tự tạo.*

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
### Mô tả chức năng
Hàm trả về danh sách các sách còn trong kho với số lượng lớn hơn 0.
### Tạo hàm

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/9c72da24-c031-465c-a375-9f7d3452eae5" />

### Mô tả chức năng
Hàm thống kê số lần mượn sách của từng sinh viên dựa trên bảng PhieuMuon.

### Gọi hàm

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/373f2d8f-1255-4348-bbba-0f817d9a6baa" />
*Chú thích:Thống kê số lần mượn của sinh viên.*

## 5. Multi-statement Table-Valued Function

### Mô tả chức năng
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
### Mô tả chức năng
Hàm thống kê số lần mượn sách của từng sinh viên dựa trên bảng PhieuMuon.
### Tạo hàm

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/a77b39de-9949-4033-957c-41cccbe7ce4f" />


### Gọi hàm

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/76175756-b5d8-40a8-bbbf-983a014c190a" />
*Chú thích:Thống kê số lần mượn sách của sinh viên.*

---
# Phần 4: Trigger và Xử lý logic nghiệp vụ

## 1. Mô tả chức năng

Khi có phiếu mượn mới (insert vào bảng PhieuMuon) thì:
- Tự động giảm số lượng sách trong bảng Sach

Ngược lại:
- Khi cập nhật số lượng sách trong bảng Sach thì kiểm tra và cập nhật lại dữ liệu liên quan

---

## 2. Trigger A → B (PhieuMuon → Sach)

### Ý tưởng
Khi mượn sách thì số lượng sách giảm đi 1.

### Code
```sql
CREATE TRIGGER trg_GiamSoLuongSach
ON PhieuMuon
AFTER INSERT
AS
BEGIN
    UPDATE Sach
    SET SoLuong = SoLuong - 1
    WHERE MaSach IN (SELECT MaSach FROM inserted);
END;
```
### Trigger A → B
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/adfcd889-4d1b-4e22-9f7c-cc2893e10291" />

### Test Trigger (giảm số lượng)
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/ae406a72-4795-4cbe-838f-39db5d6c06d4" />
*Chú thích:Khi thêm phiếu mượn, số lượng sách tương ứng giảm đi.*

## 3. Trigger B → A (Sach → PhieuMuon)

### Mô tả chức năng
Khi số lượng sách không hợp lệ (âm) thì hệ thống sẽ từ chối cập nhật.

### Code
```sql
CREATE TRIGGER trg_KiemTraSoLuong
ON Sach
AFTER UPDATE
AS
BEGIN
    IF EXISTS (SELECT * FROM inserted WHERE SoLuong < 0)
    BEGIN
        PRINT N'Lỗi: Số lượng sách không hợp lệ!';
        ROLLBACK TRANSACTION;
    END
END;
```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/1479c31b-2df4-41c2-bc32-2e9eb8230c12" />
*Chú thích: Trigger trg_KiemTraSoLuong được tạo để kiểm tra dữ liệu sau khi cập nhật. Nếu phát hiện số lượng sách âm, hệ thống sẽ rollback nhằm đảm bảo tính toàn vẹn dữ liệu.*

### Test Trigger lỗi

<img width="1156" height="651" alt="image" src="https://github.com/user-attachments/assets/fd34c42a-59dc-45a1-b481-5262688c2ce5" />
*Chú thích: Thử cập nhật số lượng sách về giá trị âm (-1). Hệ thống từ chối do vi phạm ràng buộc CHECK (SoLuong >= 0), đảm bảo tính toàn vẹn dữ liệu.*
*Không thể cập nhật do vi phạm ràng buộc dữ liệu (SoLuong >= 0).*
*Kết quả: Không thể cập nhật và hệ thống hiển thị thông báo lỗi.*

### Test Trigger hợp lệ

<img width="1917" height="1078" alt="image" src="https://github.com/user-attachments/assets/c24be8e9-7546-4072-b249-685edb67bf7f" />
*Chú thích: Cập nhật số lượng sách với giá trị hợp lệ (>= 0). Hệ thống thực hiện thành công và dữ liệu được cập nhật chính xác.*

 Ràng buộc CHECK và Trigger cùng tham gia kiểm soát dữ liệu, giúp hệ thống an toàn và tránh dữ liệu sai.
 
## 4. Quan sát và nhận xét

Khi 2 trigger hoạt động:

Trigger 1: giảm số lượng sách khi mượn
Trigger 2: kiểm tra dữ liệu khi cập nhật

Nếu không kiểm soát tốt có thể gây:

vòng lặp trigger (trigger gọi lẫn nhau)
lỗi hệ thống hoặc rollback
## 5. Kết luận

Trigger giúp:

Tự động hóa xử lý dữ liệu
Đảm bảo tính toàn vẹn dữ liệu

Tuy nhiên cần thiết kế hợp lý để tránh:

xung đột logic
vòng lặp vô hạn

---

# Phần 5: Cursor và Duyệt dữ liệu

## 1. Mô tả bài toán

Trong hệ thống quản lý thư viện, mỗi phiếu mượn có:
- Ngày mượn (NgayMuon)
- Ngày trả (NgayTra)

Từ đó có thể tính số ngày mượn.

Yêu cầu:
- Nếu số ngày mượn > 365 ngày → coi là mượn quá hạn

---

## 2. Giải bằng CURSOR

### Logic

- Duyệt từng bản ghi trong bảng PhieuMuon  
- Tính số ngày mượn cho từng phiếu  
- Nếu quá hạn thì in thông báo  

---

### Code

```sql
DECLARE @MaPM INT, @NgayMuon DATE, @NgayTra DATE, @SoNgay INT;

DECLARE cur_PhieuMuon CURSOR FOR
SELECT MaPM, NgayMuon, NgayTra FROM PhieuMuon;

OPEN cur_PhieuMuon;

FETCH NEXT FROM cur_PhieuMuon INTO @MaPM, @NgayMuon, @NgayTra;

WHILE @@FETCH_STATUS = 0
BEGIN
    SET @SoNgay = DATEDIFF(DAY, @NgayMuon, @NgayTra);

    IF @SoNgay > 365
        PRINT N'Phiếu mượn ' + CAST(@MaPM AS NVARCHAR) + N' quá hạn';

    FETCH NEXT FROM cur_PhieuMuon INTO @MaPM, @NgayMuon, @NgayTra;
END;

CLOSE cur_PhieuMuon;
DEALLOCATE cur_PhieuMuon;

```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/bff4c94c-5052-4871-9106-721a6963d7c8" />
*Cursor duyệt từng bản ghi trong bảng PhieuMuon và kiểm tra số ngày mượn.*

## 3. Giải KHÔNG dùng CURSOR
Logic
Tính số ngày mượn bằng DATEDIFF
Lọc trực tiếp các phiếu mượn quá hạn

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/46985021-e279-420d-ab2f-fa1c758bde3d" />
*Truy vấn sử dụng hàm DATEDIFF để tính số ngày mượn của từng phiếu mượn.
Sau đó áp dụng điều kiện WHERE để lọc ra các phiếu mượn có thời gian từ 365 ngày trở lên.

Kết quả trả về danh sách các phiếu mượn thỏa điều kiện mà không cần sử dụng Cursor.*

## 4. So sánh với CURSOR
*Nhận xét:*
-Cursor:
+Duyệt từng dòng dữ liệu
+Linh hoạt trong xử lý logic từng bản ghi
+Tốc độ chậm hơn
-Không dùng Cursor:
+Xử lý toàn bộ dữ liệu bằng 1 câu lệnh SQL
+Tốc độ nhanh hơn
+Code ngắn gọn, dễ hiểu

## 5. Kết luận

Trong bài toán này, việc sử dụng truy vấn SQL thông thường là đủ để giải quyết yêu cầu.
Cursor không cần thiết vì không có xử lý phức tạp theo từng dòng.

Do đó, cách không dùng Cursor là tối ưu hơn về hiệu năng và đơn giản trong triển khai.
