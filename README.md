# BT2 - QUẢN TRỊ CSDL

## Phần mở đầu

### Thông tin cá nhân
- Họ tên: (ghi tên bạn)
- Lớp: (ghi lớp bạn)
- MSSV: K235480106057
- Chủ đề: Quản lý thư viện

---

# Phần 1: Thiết kế và Khởi tạo Cấu trúc Dữ liệu

- Tên database: QuanLyThuVien_K235480106057

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
