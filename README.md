# dxos-witness-kit

Bộ dựng sẵn để **một người ngoài đội** làm *witness độc lập* cho [DX-OS Trust Core](https://github.com/PSA-OVNCR/dxos)
(đội PSA_OVNCR, OLP PMNM 2026). Không cần máy chủ: mọi thứ chạy trên GitHub Actions miễn phí.

## Witness làm gì, vì sao cần người ngoài
DX-OS ghi mọi sự kiện quan trọng vào sổ cái Merkle, định kỳ ký một "điểm chốt" (STH) và đẩy lên repo neo công khai
[dxos-transparency-log](https://github.com/PSA-OVNCR/dxos-transparency-log). Witness định kỳ:
1. lấy STH mới nhất từ máy chủ DX-OS và kiểm chữ ký;
2. so với tệp trong repo neo;
3. kiểm *consistency proof*: lịch sử cũ không bị viết lại;
4. lưu STH đã kiểm vào thư mục `state/` của **repo này**.

Nếu đội DX-OS (hoặc kẻ chiếm được cả máy chủ lẫn tài khoản quản trị repo neo) viết lại lịch sử, bản STH cũ trong
`state/` của bạn sẽ chứng minh điều đó. Vì vậy repo witness nên nằm **trong tài khoản GitHub của chính bạn**, nơi đội
không có quyền gì.

## Cài đặt (khoảng 5 phút)
1. Bấm **Use this template → Create a new repository**, chọn **tài khoản của bạn** làm owner, để Public.
2. Trong repo vừa tạo: **Settings → Secrets and variables → Actions → Variables → New repository variable**
   - `DXOS_SERVER` = địa chỉ DX-OS đội gửi cho bạn, ví dụ `https://dxos.example.vn`
3. **Settings → Actions → General**: chọn *Allow all actions*; mục *Workflow permissions* chọn *Read and write permissions*.
4. Vào tab **Actions → witness → Run workflow** để chạy lần đầu. Lần đầu báo `FIRST_RUN`; từ đó workflow tự chạy 6 giờ một lần.

Chưa đặt `DXOS_SERVER` thì workflow tự bỏ qua, không báo lỗi.

## Khi workflow đỏ
| Kết quả | Ý nghĩa |
|---|---|
| `BAD_SIGNATURE` | Chữ ký sai, hoặc khóa công khai của cùng phiên bản bị thay |
| `ANCHOR_MISMATCH` | STH máy chủ trả khác tệp trong repo neo |
| `REWRITE_DETECTED` | Cùng kích thước mà root khác, cây nhỏ đi, hoặc lịch sử đã neo bị viết lại |
| `INCONSISTENT` | Consistency proof không hợp lệ: lịch sử đã bị viết lại |

Hãy **không sửa** thư mục `state/`, chụp màn hình lần chạy đỏ và báo cho đội qua
[Issues của repo dxos](https://github.com/PSA-OVNCR/dxos/issues). Lỗi mạng hoặc máy chủ tạm tắt không phải bất thường;
lần chạy sau sẽ tự kiểm lại.

## Biến tùy chọn
| Biến | Mặc định | Ý nghĩa |
|---|---|---|
| `DXOS_REF` | `v0.1.0` | Tag/nhánh của `PSA-OVNCR/dxos` dùng để cài `dxos-witness` |
| `DXOS_ANCHOR_REPO` | `PSA-OVNCR/dxos-transparency-log` | Repo neo cần đối chiếu |

## Giấy phép
Apache-2.0 (xem `LICENSE`).
