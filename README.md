# GoToolkitConsole — Hướng dẫn sử dụng

Công cụ kiểm tra tự động cho Go project trên Windows.  
Tích hợp trực tiếp vào VS Code — nhấn một phím là chạy toàn bộ.

---

## Công cụ làm gì?

Mỗi lần chạy, công cụ thực hiện 4 bước theo thứ tự:

| Bước | Công cụ | Mục đích |
|------|---------|----------|
| 1 | `go vet` | Phát hiện lỗi logic, cú pháp phổ biến |
| 2 | `staticcheck` | Phân tích tĩnh nâng cao, phát hiện code deprecated |
| 3 | `go build` amd64 | Kiểm tra build Windows 64-bit |
| 4 | `go build` 386 | Kiểm tra build Windows 32-bit |

**Kết quả:**
- ✅ Tất cả OK → thoát im lặng, không làm phiền
- ❌ Có lỗi → ghi file log → tự động mở lên để xem

---

## Yêu cầu hệ thống

| Phần mềm | Tải về |
|----------|--------|
| **Go** (bắt buộc) | https://go.dev/dl/ |
| **VS Code** (bắt buộc) | https://code.visualstudio.com/ |
| **staticcheck** (khuyến nghị) | Xem hướng dẫn bên dưới |
| **MSYS2** (nếu dùng cgo) | https://www.msys2.org/ |

### Cài staticcheck

Mở terminal, chạy lệnh:
```
go install honnef.co/go/tools/cmd/staticcheck@latest
```

### Cài MSYS2 cho cgo (build DLL, C bindings)

1. Tải và cài MSYS2 tại https://www.msys2.org/
2. Mở **MSYS2 UCRT64**, chạy:
```
pacman -S mingw-w64-ucrt-x86_64-gcc mingw-w64-i686-gcc
```

> Nếu project không dùng cgo (không import "C"), bỏ qua bước này.

---

## Cài đặt

### Bước 1 — Chuẩn bị file

Bạn nhận được 2 file:
```
GoToolkitConsole.exe
tasks.json
```

### Bước 2 — Copy vào project

Tạo thư mục `.vscode` trong thư mục gốc project Go của bạn (nếu chưa có), rồi copy cả 2 file vào:

```
TenProject\
├── .vscode\
│   ├── GoToolkitConsole.exe   ← copy vào đây
│   └── tasks.json             ← copy vào đây
├── go.mod
├── main.go
└── ...
```

> **Lưu ý:** Thư mục `.vscode` phải nằm cùng cấp với file `go.mod`.

### Bước 3 — Mở project trong VS Code

```
File → Open Folder → chọn thư mục TenProject
```

---

## Sử dụng

### Chạy kiểm tra

Nhấn **`Ctrl + Shift + B`**

Hoặc:
1. Nhấn `Ctrl + Shift + P`
2. Gõ `Run Task`
3. Chọn **"Go: Kiểm tra toàn bộ"**

### Đọc kết quả

Terminal hiện ra output theo định dạng:

```
[INFO]  thông tin thông thường
[OK]    bước thành công
[WARN]  cảnh báo (không dừng)
[FAIL]  lỗi cần sửa
```

**Ví dụ kết quả OK:**
```
=== GO VET ===
[OK]   GO VET OK

=== STATICCHECK ===
[OK]   STATICCHECK OK

=== BUILD amd64 ===
[OK]   BUILD amd64 OK

=== BUILD 386 ===
[OK]   BUILD 386 OK

=====================================
[OK]   TAT CA OK
```

**Ví dụ kết quả có lỗi:**
```
=== STATICCHECK ===
[INFO]   crypto.go:45:12: cipher.NewCFBEncrypter has been deprecated...
[FAIL] STATICCHECK THAT BAI

=====================================
[FAIL] CO LOI - xem log
Log: C:\Users\TenUser\AppData\Local\Temp\GoToolkitChecker.log
```
→ File log tự động mở ra để xem chi tiết.

---

## File log

- **Vị trí:** `%TEMP%\GoToolkitChecker.log`  
  (thường là `C:\Users\TenUser\AppData\Local\Temp\GoToolkitChecker.log`)
- **Chỉ tạo khi có lỗi** — nếu OK không tạo file
- **Tự dọn** theo Windows Disk Cleanup, không rác project

---

## Công cụ tự tìm go.exe

Không cần cấu hình đường dẫn. Công cụ tự tìm `go.exe` theo thứ tự:

1. Registry `HKLM\SOFTWARE\Go` (Go installer chính thức)
2. Biến môi trường `GOROOT`
3. Quét toàn bộ `PATH`
4. Các vị trí phổ biến:
   - `C:\Program Files\Go\bin\go.exe`
   - `C:\Go\bin\go.exe`
   - `C:\ProgramData\chocolatey\bin\go.exe` (Chocolatey)
   - v.v.

Nếu không tìm thấy, file log sẽ hiện thông báo và link tải Go.

---

## Dùng cho nhiều project

Chỉ cần **copy 2 file** vào `.vscode\` của project mới — không cần chỉnh sửa gì:

```
ProjectA\
└── .vscode\
    ├── GoToolkitConsole.exe
    └── tasks.json

ProjectB\
└── .vscode\
    ├── GoToolkitConsole.exe
    └── tasks.json
```

Công cụ tự nhận diện thư mục project dựa vào vị trí file `go.mod`.

---

## Xử lý sự cố

| Triệu chứng | Nguyên nhân | Cách sửa |
|-------------|-------------|----------|
| `Khong tim thay go.exe` | Go chưa cài hoặc PATH chưa có | Cài Go tại go.dev/dl, khởi động lại VS Code |
| `Khong tim thay go.mod` | Mở sai thư mục trong VS Code | `File → Open Folder` → chọn đúng thư mục chứa `go.mod` |
| `Khong tim thay staticcheck` | Chưa cài | Chạy `go install honnef.co/go/tools/cmd/staticcheck@latest` |
| Build 386/amd64 thất bại với lỗi gcc | MSYS2 chưa cài hoặc thiếu GCC | Cài MSYS2 và GCC theo hướng dẫn ở trên |
| Task không xuất hiện trong VS Code | `tasks.json` đặt sai chỗ | Kiểm tra `.vscode\tasks.json` nằm đúng trong thư mục project |

---

## Cấu trúc thư mục chuẩn

```
TenProject\                  ← mở thư mục này trong VS Code
├── .vscode\
│   ├── GoToolkitConsole.exe
│   └── tasks.json
├── go.mod                   ← công cụ tìm file này để xác định project
├── go.sum
├── main.go
└── ...các file .go khác...
```

---

*GoToolkitConsole — xây dựng bằng Delphi 13.1*  
*Yêu cầu: Windows 10/11 x64, Go 1.18+*
