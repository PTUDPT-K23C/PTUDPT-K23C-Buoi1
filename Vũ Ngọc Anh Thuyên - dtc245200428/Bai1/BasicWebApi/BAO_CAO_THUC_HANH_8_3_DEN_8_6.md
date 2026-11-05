# Báo cáo thực hành 8.3–8.6 – BasicWebApi

## 1. Thông tin dự án

- Dự án: `BasicWebApi` – ASP.NET Core Minimal API, .NET 10.
- Project: `src/BasicWebApi/BasicWebApi.csproj`.
- API chạy HTTP trên `http://localhost:5081`.
- File chính: `src/BasicWebApi/Program.cs`; request kiểm thử: `requests.http`.
- Đã bổ sung `src/BasicWebApi/Properties/launchSettings.json` để profile debug `BasicWebApi` dùng cổng 5081 và môi trường Development.

[CHÈN ẢNH MINH CHỨNG 1 – Cấu trúc dự án và Program.cs]

## 2. Mục 8.3 – Kiểm thử bằng REST Client

Đã cài REST Client trong Antigravity IDE và dùng `requests.http` kiểm thử. Các request chính gồm GET `/`, GET lời chào (tiếng Việt/Anh), POST hợp lệ và POST tên rỗng. Đã bổ sung thêm request tiếng Nhật cho mục 8.5.

| Request | Kết quả mong đợi | Kết quả đã quan sát | Trạng thái |
|---|---|---|---|
| GET `/` | 200 OK, `application` là `BasicWebApi` | 200 OK, JSON có `application: BasicWebApi` | PASS |
| GET `/hello/An?language=vi` | 200 OK | `Xin chào, An!` | PASS |
| GET `/hello/An?language=en` | 200 OK | `Hello, An!` | PASS |
| POST `/greetings`, body `{"name":"Binh"}` | 201 Created, có Location | 201 Created; Location `/greetings/{id}` | PASS |
| POST `/greetings`, body `{"name":""}` | 400 Bad Request, lỗi tên rỗng | 400 và `Name must not be empty.` | PASS |
| GET `/hello/An?language=ja` (bổ sung) | 200 OK | `Konnichiwa, An!` | PASS |

ID lời chào tăng theo số lần POST trong từng tiến trình API; vì vậy ID có thể khác ví dụ trong tài liệu.

[CHÈN ẢNH MINH CHỨNG 2 – GET / và phản hồi JSON]

[CHÈN ẢNH MINH CHỨNG 3 – POST hợp lệ: 201 Created và Location]

[CHÈN ẢNH MINH CHỨNG 4 – POST tên rỗng: 400 Bad Request]

## 3. Mục 8.4 – Đọc code trong Program.cs

**Câu 1.** `builder.Services.AddSingleton<GreetingService>()` phải đứng trước `builder.Build()`. Các dịch vụ cần được đăng ký trước khi ứng dụng được tạo từ builder.

**Câu 2.** Trong `/hello/{name}?language=en`, `name` được lấy từ route (đường dẫn), còn `language` được lấy từ query string sau dấu `?`. Route Parameter là một phần của đường dẫn; Query Parameter là dữ liệu bổ sung trong query string.

**Câu 3.** POST tạo lời chào mới nên trả `201 Created`. `200 OK` báo yêu cầu thành công thông thường; `201 Created` báo tài nguyên mới đã được tạo. Header `Location` chỉ URI của tài nguyên vừa tạo. Dùng `Results.Ok` cho kết quả thành công thông thường, `Results.Created` khi tạo tài nguyên, và `Results.BadRequest` khi dữ liệu yêu cầu không hợp lệ.

**Đoạn ghi nhật ký:** Trong chương trình, tôi đăng ký `GreetingService` bằng Singleton trước khi gọi `builder.Build()` để ứng dụng tạo được đầy đủ dịch vụ. Ở endpoint `/hello/{name}`, `name` lấy từ đường dẫn còn `language` lấy từ query string. Khi POST tạo lời chào thành công, API trả `201 Created` và header `Location` cho biết URI của lời chào mới. `200 OK` dùng cho xử lý thành công thông thường, còn `400 Bad Request` dùng khi dữ liệu gửi lên không hợp lệ.

[CHÈN ẢNH MINH CHỨNG 5 – Các endpoint và đăng ký service trong Program.cs]

## 4. Mục 8.5 – Bổ sung tiếng Nhật

Trong xử lý ngôn ngữ của GET `/hello/{name}`, đã thêm nhánh kiểm tra `ja` không phân biệt chữ hoa/chữ thường và trả `Konnichiwa, {name}!`. Nhánh tiếng Anh và cách mặc định tiếng Việt được giữ nguyên. Request `GET /hello/An?language=ja` đã trả 200 OK với `{"message":"Konnichiwa, An!"}`.

[CHÈN ẢNH MINH CHỨNG 6 – Request tiếng Nhật và phản hồi 200]

## 5. Mục 8.6 – Debug API

Antigravity không có giao diện debug C# phù hợp, nên dùng VS Code với C# Dev Kit. Đặt breakpoint ở dòng `var greeting = service.Create(request.Name.Trim());` trong `Program.cs`, chọn profile `C#: BasicWebApi`, chạy debug và gửi POST `/greetings` với `{"name":"Binh"}`.

Breakpoint đã dừng tại dòng 34. Trong Variables, `request` có `Name = Binh`, `service` là `GreetingService`, và `greeting` chưa có giá trị trước khi chạy dòng tạo. Sau F10, `greeting` có `Id`, `Name = Binh` và thời điểm tạo. Tiếp tục bằng F5, request trả 201 Created.

- F10: Step Over, chạy qua dòng hiện tại.
- F11: Step Into, đi vào hàm được gọi.
- F5: Continue, tiếp tục chạy chương trình.
- Shift + F5: Stop Debugging, dừng phiên debug.

Luồng xử lý: HTTP POST → endpoint `/greetings` → kiểm tra `request.Name` → gọi `GreetingService.Create(...)` → trả `201 Created` và `Location`.

[CHÈN ẢNH MINH CHỨNG 7 – Breakpoint dừng tại dòng 34 và Variables]

[CHÈN ẢNH MINH CHỨNG 8 – greeting sau F10 hoặc phản hồi 201 sau F5]

## 6. Các lỗi thường gặp và cách xử lý

### Cổng đã được sử dụng

Thông báo `Failed to bind to address ... address already in use` có nghĩa là tiến trình khác đang giữ cổng. Dừng API cũ bằng Ctrl+C trong terminal tương ứng hoặc dừng phiên debug bằng Shift+F5 trong VS Code; sau đó chạy lại. Nếu cần dùng cổng 5082, đổi URL chạy API và cập nhật biến `api`/URL trong `requests.http` cho trùng nhau. Trong lần debug này, có một tiến trình BasicWebApi đang giữ cổng 5081; cần dùng đúng tiến trình đó thay vì khởi động thêm bản sao.

### 415 Unsupported Media Type

Kiểm tra request POST có header `Content-Type: application/json` và body là JSON hợp lệ, ví dụ `{"name":"Binh"}`. Với REST Client, đặt header ngay trên request POST.

### Không thấy Send Request

Kiểm tra extension REST Client đã cài và bật, file có đuôi `.http` hoặc `.rest`, và cú pháp request hợp lệ. Nếu vẫn không có CodeLens, bật lại cửa sổ IDE/reload window rồi mở lại file.

## 7. Kết luận

Đã kiểm thử các endpoint chính, bổ sung và xác nhận lời chào tiếng Nhật, đọc/giải thích được các phần quan trọng trong `Program.cs`, và debug thành công endpoint POST với breakpoint, Variables và bước F10/F5. Các vị trí ảnh minh chứng phía trên được để trống để sinh viên tự chụp ảnh thực tế và bổ sung.
