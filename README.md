# 🔧 GoToolkitConsole

> **Công cụ kiểm tra Go project tự động — tích hợp trực tiếp vào VS Code**  
> Viết bằng Delphi · Chạy trên Windows · Không cần cấu hình

---

## ✨ Tính năng

| | Tính năng | Mô tả |
|---|---|---|
| 🔍 | **go vet** | Phát hiện lỗi logic và cú pháp phổ biến |
| 🧪 | **staticcheck** | Phân tích tĩnh nâng cao, phát hiện code deprecated |
| 🏗️ | **Build amd64** | Kiểm tra build Windows 64-bit |
| 🏗️ | **Build 386** | Kiểm tra build Windows 32-bit |
| 📄 | **Auto log** | Có lỗi → ghi log → tự mở lên. Không lỗi → thoát im lặng |
| 🔎 | **Tự tìm go.exe** | Không cần hardcode đường dẫn |
| 📦 | **Portable** | Copy vào project nào cũng chạy ngay |

---

## 🖥️ Demo

**Khi tất cả OK** — terminal tự đóng, không làm phiền:

```
[INFO] === GO TOOLKIT CHECKER ===
[INFO] Project : D:\MyApp\GoLibrary
[INFO] go.exe  : C:\Program Files\Go\bin\go.exe

[INFO] === GO VET ===
[OK]   GO VET OK

[INFO] === STATICCHECK ===
[OK]   STATICCHECK OK

[INFO] === BUILD amd64 ===
[OK]   BUILD amd64 OK

[INFO] === BUILD 386 ===
[OK]   BUILD 386 OK

=====================================
[OK]   TAT CA OK
```

**Khi có lỗi** — log chi tiết tự động mở ra:

```
[INFO] === STATICCHECK ===
[INFO]   crypto.go:45:12: cipher.NewCFBEncrypter has been deprecated
         since Go 1.24 — use AEAD mode instead. (SA1019)
[FAIL] STATICCHECK THAT BAI

=====================================
[FAIL] CO LOI - xem log
Log: C:\Users\...\AppData\Local\Temp\GoToolkitChecker.log
```

---

## 🚀 Cài đặt

### Yêu cầu

- 🪟 Windows 10 / 11
- 🐹 [Go 1.18+](https://go.dev/dl/)
- 💻 [VS Code](https://code.visualstudio.com/)
- 🔬 [staticcheck](https://staticcheck.dev/) *(khuyến nghị)*

```bash
# Cài staticcheck
go install honnef.co/go/tools/cmd/staticcheck@latest
```

### Cài đặt chỉ 2 bước

**Bước 1** — Tải 2 file:

```
GoToolkitConsole.exe
tasks.json
```

**Bước 2** — Copy vào thư mục `.vscode\` của project:

```
📁 MyGoProject\
├── 📁 .vscode\
│   ├── ⚙️  GoToolkitConsole.exe   ← copy vào đây
│   └── 📋 tasks.json              ← copy vào đây
├── 📄 go.mod
├── 📄 main.go
└── ...
```

> ✅ Xong! Không cần cài thêm gì, không cần cấu hình.

---

## ⌨️ Sử dụng

Mở project trong VS Code, nhấn:

```
Ctrl + Shift + B
```

Hoặc `Ctrl+Shift+P` → **"Run Task"** → chọn **"Go: Kiểm tra toàn bộ"**

---

## 🧠 Cách hoạt động

```
Ctrl+Shift+B
      │
      ▼
GoToolkitConsole.exe
      │
      ├─► go vet ./...
      │
      ├─► staticcheck ./...
      │
      ├─► go build (amd64)
      │
      └─► go build (386)
            │
            ├── ✅ Tất cả OK  →  thoát im lặng (exit 0)
            │
            └── ❌ Có lỗi    →  ghi %TEMP%\GoToolkitChecker.log
                               →  tự mở file log (exit 1)
```

---

## 🔎 Tự động tìm go.exe

Không cần hardcode đường dẫn. Công cụ tìm `go.exe` theo thứ tự:

```
1️⃣  Registry   HKLM\SOFTWARE\Go         (Go installer chính thức)
2️⃣  Env var    %GOROOT%\bin\go.exe
3️⃣  PATH       quét toàn bộ thư mục trong PATH
4️⃣  Fallback   C:\Program Files\Go\bin\go.exe
               C:\Go\bin\go.exe
               C:\ProgramData\chocolatey\bin\go.exe  (Chocolatey)
               ...
```

---

## 📁 File log

| Thuộc tính | Chi tiết |
|---|---|
| 📍 Vị trí | `%TEMP%\GoToolkitChecker.log` |
| 🕐 Tạo khi | Chỉ khi có lỗi |
| 🗑️ Dọn dẹp | Tự động theo Windows Disk Cleanup |
| 🔓 Mở bằng | App mặc định của hệ thống |

> Không tạo file rác trong thư mục project.

---

## 📦 Dùng cho nhiều project

Copy 2 file vào `.vscode\` của bất kỳ project nào — **không cần chỉnh sửa gì**:

```
📁 ProjectA\
└── 📁 .vscode\
    ├── ⚙️  GoToolkitConsole.exe
    └── 📋 tasks.json

📁 ProjectB\
└── 📁 .vscode\
    ├── ⚙️  GoToolkitConsole.exe
    └── 📋 tasks.json
```

Công cụ tự nhận diện thư mục project dựa vào vị trí file `go.mod`.

---

## ❓ Xử lý sự cố

<details>
<summary><b>🔴 Không tìm thấy go.exe</b></summary>

Go chưa được cài hoặc chưa có trong PATH.  
→ Tải và cài Go tại https://go.dev/dl/  
→ Khởi động lại VS Code sau khi cài

</details>

<details>
<summary><b>🔴 Không tìm thấy go.mod</b></summary>

VS Code đang mở sai thư mục.  
→ `File → Open Folder` → chọn thư mục chứa `go.mod`

</details>

<details>
<summary><b>🟡 staticcheck bị bỏ qua</b></summary>

staticcheck chưa được cài.  
→ Chạy: `go install honnef.co/go/tools/cmd/staticcheck@latest`

</details>

<details>
<summary><b>🔴 Build thất bại — lỗi gcc</b></summary>

Project dùng cgo nhưng chưa cài GCC.  
→ Cài [MSYS2](https://www.msys2.org/) và chạy:

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc mingw-w64-i686-gcc
```

</details>

<details>
<summary><b>🔴 Task không xuất hiện trong VS Code</b></summary>

`tasks.json` đặt sai vị trí.  
→ Kiểm tra file nằm tại `TenProject\.vscode\tasks.json`

</details>

---

## 🛠️ tasks.json

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Go: Kiểm tra toàn bộ",
      "type": "process",
      "command": "${workspaceFolder}\\.vscode\\GoToolkitConsole.exe",
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "presentation": {
        "reveal": "always",
        "panel": "dedicated",
        "clear": true,
        "showReuseMessage": false
      },
      "problemMatcher": []
    }
  ]
}
```

---

## 📋 Yêu cầu hệ thống

| | |
|---|---|
| 🪟 OS | Windows 10 / 11 x64 |
| 🐹 Go | 1.18 trở lên |
| 💻 VS Code | Bất kỳ phiên bản nào |
| 🔬 staticcheck | Tùy chọn, khuyến nghị |
| 🔨 GCC/MSYS2 | Chỉ cần nếu project dùng cgo |

---

## 📜 License

MIT — tự do sử dụng, chỉnh sửa và phân phối.

---

<div align="center">

**Nếu công cụ hữu ích, hãy ⭐ star repo này!**

*Xây dựng bằng ❤️ và Delphi 13.1*

</div>
