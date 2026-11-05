# Nhật ký thực hành Bài 1 – Phát triển phân tán

**Ngày thực hiện:** 05/11/2026  
**Dự án:** BasicWebApi, MepDemo và MiniDistributedSystem

## 1. Môi trường

Máy đã có .NET SDK 10.0.202 và 10.0.403. Các project C# trong thư mục Bài 1 đặt `TargetFramework` là `net10.0`.

## 2. Chạy các chương trình mẫu

- `MepDemo`: chạy toàn bộ chương trình. Console lần lượt minh họa Request–Response, One-way, Queue-based, Publish–Subscribe và Streaming/Push. Ví dụ timeout của Request–Response cũng được hiển thị.
- `BasicWebApi`: chạy tại `http://localhost:5081`; kiểm thử GET trang chủ, lời chào tiếng Việt/Anh/Nhật và POST hợp lệ/không hợp lệ.
- `MiniDistributedSystem`: `Submission.Api` lắng nghe tại `http://localhost:5000`. Cổng này đã được một tiến trình `Submission.Api` của chính dự án sử dụng, nên mình dùng tiến trình đang chạy thay vì khởi động thêm bản thứ hai. Chạy `Submission.Client` thành công: nhận danh sách hai bài tập, POST lượt nộp và nhận trạng thái `received` cùng mã lượt nộp.

## 3. Kết quả BasicWebApi

REST Client trả 200 cho GET `/`, các ngôn ngữ `vi`, `en`, `ja`; POST tên `Binh` trả 201 Created kèm Location; POST tên rỗng trả 400 Bad Request với thông báo lỗi. Đã debug POST bằng VS Code/C# Dev Kit: breakpoint dừng tại dòng gọi `GreetingService.Create`, xem được `request`, `service`, rồi dùng F10 để tạo `greeting` và F5 để tiếp tục.

Báo cáo chi tiết các mục 8.3–8.6: [BAO_CAO_THUC_HANH_8_3_DEN_8_6.md](../BAO_CAO_THUC_HANH_8_3_DEN_8_6.md). Ảnh minh chứng thực tế nằm trong `docs/anh-minh-chung/`.

## 4. Nộp bài

Mã nguồn và tài liệu được lưu trong thư mục sinh viên trên fork cá nhân. Tag Git là `bai1`. Pull Request #3 đã gửi tới repository chính thức và đang chờ xét duyệt: <https://github.com/thumayduong/PTUDPT-K23C/pull/3>.
