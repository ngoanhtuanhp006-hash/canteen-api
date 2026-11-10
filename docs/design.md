# Thiết kế Canteen API

## 1. Tài nguyên MenuItem
| Trường | Kiểu | Ràng buộc | Ví dụ |
|---|---|---|---|
| id | int | Hệ thống tự sinh | 1 |
| name | str | 2–100 ký tự, duy nhất không phân biệt hoa thường (BR1) | Bún cá Hải Phòng |
| category | enum | com / bun_pho / do_uong / an_vat (BR3) | bun_pho |
| price | int | 0 < giá ≤ 200000, bội số của 1000 (BR2) | 30000 |
| available | bool | Mặc định true (BR4) | true |
| updated_at | datetime | Hệ thống tự cập nhật, chỉ đọc | 2026-10-20T11:30:00 |

## 2. Đặc tả endpoint
| Phương thức | URI | User story | Thành công | Lỗi |
|---|---|---|---|---|
| GET | /menu-items | US1 (có query) | 200 | 422 |
| GET | /menu-items/{id} | US2 | 200 | 404, 422 |
| POST | /menu-items | US3 | 201 + Location | 409, 422 |
| PUT | /menu-items/{id} | US4 (toàn bộ) | 200 | 404, 409, 422 |
| PATCH | /menu-items/{id} | US4 (một phần) | 200 | 404, 409, 422 |
| DELETE | /menu-items/{id} | US5 | 204 | 404 |

## 3. Query params của GET /menu-items
- `category`: com | bun_pho | do_uong | an_vat
- `available`: true | false
- `max_price`: số nguyên > 0
- `sort`: id | price | name (mặc định id)
- `order`: asc | desc (mặc định asc)

Ví dụ: `/menu-items?category=do_uong&available=true&sort=price&order=desc`

## 4. Quy tắc nghiệp vụ và mã lỗi
| Mã | Quy tắc | Mã lỗi |
|---|---|---|
| BR1 | Tên món duy nhất (không phân biệt hoa thường) | 409 Conflict |
| BR2 | Giá > 0, ≤ 200.000, bội số 1.000 | 422 |
| BR3 | Loại món thuộc 4 giá trị cho phép | 422 |
| BR4 | Món hết vẫn giữ, chỉ đổi available = false (dùng PATCH) | — |

Ngoài ra: id ≤ 0 → 422; id không tồn tại → 404.
