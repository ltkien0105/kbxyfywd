# Micro Client Kabu Tây Du Phù Ảnh

[![Version](https://img.shields.io/badge/version-1.11-blue.svg)](https://github.com/lllcc666/kbxyfywd)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-lightgrey.svg)](https://github.com/lllcc666/kbxyfywd)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

Tiện ích trò chơi trên desktop Windows dựa trên WebView2, được triển khai sử dụng Win32 C++17, ATL/WebBrowser, MinHook và các tài nguyên nhúng.

## Đặc điểm dự án

- Sử dụng WebView2 để lưu trữ giao diện công cụ, tài nguyên frontend từ `resources/ui.html`
- Sử dụng ATL `CAxWindow` + `IWebBrowser2` để chứa trang trò chơi
- Cung cấp khả năng chặn gói tin mạng, phân giải giao thức, tự động hóa sự kiện, kết nối dữ liệu v.v.
- Nhúng các tài nguyên HTML, WebView2Loader, zlib vào file thực thi trong quá trình build
- Môi trường phát triển chính là Windows x64 + MSVC + CMake

## Công nghệ sử dụng

| Thành phần | Mô tả |
|------|------|
| Ngôn ngữ | C++17 |
| Framework Desktop | Win32 API |
| Giao diện | WebView2 + HTML/CSS/JavaScript |
| Bộ chứa trò chơi | ATL / WebBrowser |
| Hook | MinHook |
| Nén/Tải | zlib / MemoryModule |
| Hệ thống Build | CMake 3.16+ |
| Quản lý thư viện | vcpkg |

## Yêu cầu hệ thống

- Windows 10/11 x64
- Visual Studio 2019 hoặc 2022
- CMake 3.16 trở lên
- Python 3, gọi được trên Terminal bằng lệnh `python3`
- vcpkg

## Thiết lập Build

Do `CMakeLists.txt` trong thư mục gốc hiện đang hard-code đường dẫn:

```text
d:/AItrace/CE/.trae/vcpkg-master
```

Nên nếu đường dẫn trên máy tính của bạn hoàn toàn khác, hãy thiết lập lại nó hoặc thay đổi file cấu hình cho phù hợp với CMake.

### Tạo Project

```powershell
cmake -S . -B build_new -G "Visual Studio 17 2022" -A x64
```

### Biên dịch Release

```powershell
cmake --build build_new --config Release
```

### Chạy Chương trình

```powershell
.\build_new\bin\Release\WebView2Demo.exe
```

## Cấu trúc Thư mục

```text
kbwebui/
├─ src/
│  ├─ app/                  # Điểm vào chương trình và thiết lập cửa sổ chính
│  ├─ core/                 # Kết nối, hàm tiện ích, cấu trúc luồng thông điệp, Data Interceptor
│  ├─ hook/                 # Logic chính của hệ thống Hook và thực thi Auton
│  └─ protocol/             # Hệ thống Parser đọc và viết Packet
├─ include/
│  ├─ activities/           # Các sự kiện - Modules
│  ├─ core/                 # Các Component cốt lõi (Header)
│  ├─ hook/                 # Cổng kết nối với Public Head Hook
│  ├─ internal/             # Máy trạng thái (State machine) cấu trúc nội bộ, Timeout Listener 
│  └─ protocol/             # Loại và Trình bao liên quan tới các giao thức mạng
├─ resources/               # HTML UI tĩnh, biểu tượng và trình xử lý RC
├─ embedded/                # Trình Header nhúng / Embedded
├─ data/                    # Database XML Local (Bộ đệm)
├─ scripts/                 # Scrips công cụ (Python)
├─ swf_cache/               # Download cache (Không được xác định là nguồn)
├─ build_new/               # Mục lưu Output build (Local)
├─ CMakeLists.txt
├─ README.md
└─ AGENTS.md
```

## Chú thích các file cốt lõi

- `src/app/demo.cpp`
  - Điểm vào, vòng đời cửa sổ, khởi tạo WebView2, luồng UI toàn cục
- `src/hook/wpe_hook.cpp`
  - Vòng đời Hook, chặn gói tin, phân phối phản hồi, logic tự động hóa chính
- `src/hook/wpe_hook_helpers.cpp`
  - Triển khai phụ trợ lớp Hook
- `src/protocol/packet_parser.cpp`
  - Phân giải giao thức, xử lý Opcode/Params, logic giải nén
- `src/protocol/packet_builder.cpp`
  - Hỗ trợ tạo cấu trúc gói tin (Packet Build)
- `src/core/ui_bridge.cpp`
  - Kết nối (bridge) từ C++ sang JavaScript frontend
- `src/core/web_message_handler.cpp`
  - Cổng gọi tin nhắn từ phía trình duyệt Web về tiến trình thực thi Native
- `src/core/data_interceptor.cpp`
  - Ngắt và sửa đổi tài nguyên phản hồi qua HTTP
- `include/activities/*.h`
  - Định nghĩa sự kiện (Event), tình trạng thực thi và khởi tạo điểm vào hàm Tự động hóa
- `include/internal/*.h`
  - Trạng thái nội bộ (Structure Internal), hàng đợi và State Machine

## Tài nguyên nguồn và File xuất bảo

### Nguồn dự án 

- `resources/ui.html`
- `resources/app.rc`
- `resources/app.ico`

### Quá trình sinh cấu trúc Build 

Các file sau tự sinh ra trong quá trình Build, không khuyến khích sửa tay trực tiếp:

- `embedded/ui_html.h`
- `embedded/webview2loader_data.h`
- `embedded/zlib_data.h`

### Trình sinh Header nhúng được cấu hình trong Repository

Các file dưới đây được đặt ở chuyên mục `embedded/`, nhưng không được sinh từ tiến trình Cmake tuỳ biến hiện hành:

- `embedded/minhook_data.h`
- `embedded/speed_x64_data.h`
- `embedded/minizip_helper.h`

## Script của Python 

- `scripts/embed_html.py`
  - Ánh xạ file mã nguồn Web `resources/ui.html` vào code nhúng
- `scripts/embed_dll.py`
  - Ánh xạ File DLL  vào code nhúng
- `scripts/download_swf.py`
  - Download các SWF Asset trò chơi
- `scripts/fetch_activities.py`
  - Lấy thông tin Activity Cache Scripting Data 

## Giải thích Cấu trúc Giao thức

Định dạng Packet Payload cơ bản của toàn Project lúc này sẽ theo cấu trúc:

```text
Magic(2) + Length(2) + Opcode(4) + Params(4) + Body
```

- Magic gói tải thường: `0x5344`
- Magic gói tải nén: `0x5343`
- Tham chiếu giá trị tuân theo tiến trình Endian xử lý theo chuẩn nhỏ (Little Endian).

## Gợi ý Phát triển

### Thêm Tính năng Sự kiện Nhiệm vụ Mới

Thông thường sẽ đi theo thứ tự sau:

1. Bổ sung các Value Constants (Hằng số), Opcode, Datastructures tại Protocol.
2. Thêm mới Function Declaretion Component (Tuyên bố phương thức) trong `include/activities/`
3. Cấp phát cơ cấu Trạng thái Cục bộ và Bộ chờ tại `include/internal/` (Nếu thấy sẽ thiết lập).
4. Thống nhất cơ cấu Khởi - Trả yêu cầu chạy Event / Update Tự động hóa cho Hook, làm việc ngay tại file `src/hook/wpe_hook.cpp`
5. Khởi tạo điểm gọi cho tính năng và xuất qua UI tại lớp `src/core/web_message_handler.cpp` hoặc `src/app/demo.cpp`.

### Sửa đổi Frontend Framework UI 

- Tập trung sửa duy nhất file tại `resources/ui.html`.
- Gọi một Build Cycle hoàn toàn mới để cập nhật ra `embedded/ui_html.h`.
- Tránh tuyệt nhiên sửa đổi tay với file Target Config `embedded/ui_html.h`.

### Sửa đổi cho Component Configuration Tool (CMakes)

- Project File đầu vào và Component Implementations giờ sẽ build thuần cục bộ tại Source `src/`.
- File Header tĩnh đóng vào Folder con tại `include/`.
- Nếu cập nhật Component Sources vào Project C++, bắt buộc phải thao tác và Update `CMakeLists.txt` tương ứng.

## Phương thức Xác minh Logic 

Dự án hiện trạng không bố trí hệ thống Test Scripts Testing Framework. Vận hành xác nhận chạy qua bộ Toolings sau:

- Biên dịch lại Project: `cmake --build build_new --config Release`
- Chạy hệ thống: `.\build_new\bin\Release\WebView2Demo.exe`
- Kiểm tra tính ổn định Rendering WebView UI.
- Verify Hooks Injection hoạt động có còn xử lý Payload qua Responses và Action Triggers đúng ý theo kịch bản không.

## Sự cố Phổ biến 

### Build báo lỗi "Cannot Find Requirement Dependency" VCPKG

Việc báo lỗi VCPKG Root chứng tỏ tiến trình cấu hình không xác định địa chỉ chính xác, bạn sẽ phải dò File Source `CMakeLists.txt` để chắc chắn địa chỉ giá trị `VCPKG_ROOT` không xung đột với bộ Build Dependencies VCPKG chuẩn xác cài với máy bạn.