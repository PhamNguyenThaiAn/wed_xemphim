# Cinema Booking — Bộ khung tích hợp TV1

PHP thuần + MySQL/InnoDB/utf8mb4 + HTML/CSS + TypeScript, chạy trên Laragon.

**Bắt đầu tại [docs/INTEGRATION.md](docs/INTEGRATION.md)**: cấu trúc thư mục, phạm vi sở hữu module, API/Session contract, luồng Guest chọn ghế → login → xác nhận, khóa tài khoản và audit.

## Chạy dự án

1. Đặt repo tại `C:\laragon\www\cinema-booking`; web root Laragon trỏ vào **public/**.
2. MySQL 8.0.16+, PHP có PDO MySQL, mbstring và Argon2id. Đã kiểm thử MySQL 8.4.3 / PHP 8.3.33.
3. Database mới: import một lần `database/cinema_booking_complete.sql`, sau đó `database/server_audit_support.sql`. Không import lại nếu database đã có dữ liệu.
4. Kết nối ở **config/database.php**, hoặc CINEMA_DB_HOST/PORT/NAME/USER/PASS. Database chuẩn là `cinema_booking_br`.
5. `npm ci` rồi `npm run build` khi sửa TS/CSS/images. JS đã build nằm ở public/assets.
6. Laragon Start All rồi mở domain của dự án. Có thể thử bằng:

```powershell
& 'C:\laragon\bin\php\php-8.3.33-Win32-vs16-x64\php.exe' -S localhost:8001 -t public
```

Mở `http://localhost:8001/`. Khách chọn ghế tại `/server.php?route=%2Fshowtimes%2F1%2Fseats`.

Tài khoản demo: `admin@cinema.com`, `an@example.com`, `binh@example.com`; mật khẩu `Phim@2026`. Web thật dùng tài khoản MySQL riêng gán cinema_backend_role thay cho root; xem hướng dẫn server/README.md.

## Nguồn chuẩn

- `config/`, `core/`, `includes/`: TV1 quản lý.
- `admin/<resource>/routes.php`, views/, controllers/: điểm ghép UC10–14.
- `api/<module>/routes.php`: module auth/catalog/account/booking.
- `assets/css`, `assets/ts`, `assets/images`: source; assets/js và public/assets là generated.
- `docs/contracts/routes.json`: manifest tự xuất; `docs/contracts/session.schema.json` và `assets/ts/contracts.ts`: contract v1.
- `server/core`, `server/config`, header cũ: shim tương thích, không chỉnh nguồn tại đó.
- `legacy/`, `src/`, `ts/`: mã cũ để đối chiếu, không dùng schema/session cũ để ghép module mới.

`public/index.php` nay dùng chung router tích hợp. Các đường dẫn login.php/register.php/profile.php/logout.php/server.php vẫn hoạt động; API chuẩn qua `/api/index.php?route=...`, `/api.php?route=...` tương thích. Không dùng action= của API cũ.

## Tài liệu chức năng

- [Hướng dẫn server](server/README.md)
- [UC04 — Đăng ký](server/UC04.md)
- [UC05 — Đăng nhập/Đăng xuất](server/UC05.md)
- [UC06 — Tài khoản cá nhân](server/UC06.md)
- [SQL và dữ liệu mẫu](database/README_cinema_booking.md)

## Kiểm thử

```powershell
npm run build
node assets/ts/booking-draft_checks.mjs
& 'C:\laragon\bin\php\php-8.3.33-Win32-vs16-x64\php.exe' server/tests/route_contract_checks.php
& ./server/tests/run_server_checks.ps1
```

Test HTTP dùng DB/role/account riêng rồi tự dọn; không sửa dữ liệu tài khoản thật. Runner mặc định Laragon MySQL local 3306, root chưa đặt mật khẩu; chỉnh cấu hình test nếu máy khác.
