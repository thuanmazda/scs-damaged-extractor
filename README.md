# Hướng dẫn fix version mod ETS2 bị khóa/protected

## Giới thiệu

Tài liệu này hướng dẫn cách:

- Extract mod `.scs` bị protected
- Tìm file version compatibility
- Chỉnh `compatible_versions`
- Reimport lại vào file mod gốc bằng QuickBMS
- Khắc phục lỗi thường gặp khi reimport

Áp dụng cho:

- Euro Truck Simulator 2
- Các mod `.scs` dạng protected / encrypted nhẹ
- Mod dùng archive `SCS#` hoặc `[hafs]`

---

# Công cụ cần thiết

## 1. QuickBMS

Tải:

https://aluigi.altervista.org/quickbms.htm

---

## 2. HxD Hex Editor

Tải:

https://mh-nexus.de/en/hxd/

---

# Bước 1 — Extract file mod `.scs`

Giả sử mod:

```text
ThacoCruizer.scs
```

Dùng script `.bms` phù hợp rồi chạy (Script có thể download từ SourceCode file 'scs.bms':

```bash
quickbms.exe script.bms ThacoCruizer.scs output
```

Sau khi extract sẽ có dạng:

```text
00001ea3.dat
00001ea4.dat
...
```

Đây là điều bình thường với mod protected.

---

# Bước 2 — Tìm file chứa version

Mở CMD trong thư mục output:

```bash
findstr /S /I "compatible_versions" *.*
```

Hoặc:

```bash
findstr /S /I "package_version" *.*
```

Kết quả ví dụ:

```text
00001ab4.dat: compatible_versions[]: "1.54.*"
```

---

# Bước 3 — Sửa version đúng cách

## KHÔNG dùng Notepad++ để save

Vì dễ:

- thêm newline
- đổi encoding
- lệch byte size

Khiến QuickBMS báo:

```text
new size: 186
old size: 185
```

---

## Cách đúng: dùng HxD

### Mở file `.dat` bằng HxD

Search:

```text
1.54.*
```

Sau đó overwrite trực tiếp:

```text
1.54.* -> 1.55.*
```

Quan trọng:

- Không thêm ký tự
- Không Enter
- Không Insert mode
- Phải giữ nguyên kích thước file

Dưới HxD phải hiện:

```text
OVR
```

Không phải:

```text
INS
```

---

# Bước 4 — Reimport lại mod gốc

Dùng:

```bash
quickbms.exe -w -r script.bms ThacoCruizer.scs output
```

Nếu bị lỗi size mismatch thử:

```bash
quickbms.exe -w -r -r script.bms ThacoCruizer.scs output
```

Hoặc:

```bash
quickbms.exe -w -r -r -r script.bms ThacoCruizer.scs output
```

---

# Lưu ý quan trọng

## Không nên:

```text
extract -> zip -> rename .scs
```

Với mod protected:

- đã mất filesystem gốc
- mất path mapping
- game sẽ không load được

---

# Fix lỗi sound FMOD lúc có lúc mất

## Nguyên nhân phổ biến

- Sound bank cũ
- FMOD runtime incompatibility
- Event path lỗi thời
- Audio channel overflow

---

## Cách xử lý nên thử

### 1. Xóa cache ETS2

Xóa:

```text
Documents\Euro Truck Simulator 2\cache
Documents\Euro Truck Simulator 2\shader_cache
```

---

### 2. Tắt mod âm thanh khác

Tắt:

- Sound Fixes Pack
- Traffic sound
- Engine sound mods khác

---

### 3. Kiểm tra log

Mở:

```text
Documents\Euro Truck Simulator 2\game.log.txt
```

Search:

```text
fmod
bank
event
```

---

## Nếu vẫn lỗi

Khả năng cao:

```text
.bank không còn tương thích ETS2 hiện tại
```

Khi đó cần:

- thay sound mod mới
- hoặc rebuild FMOD project nếu có source `.fspro`

---

# Ghi chú

Tài liệu này dành cho mục đích học tập và nghiên cứu modding ETS2.

Hãy tôn trọng quyền tác giả của modder và tránh reupload các mod private/trả phí mà chưa được cho phép.
