# Báo cáo Bài 3 (Session 5): Xử lý xung đột phức tạp trong quá trình Rebase

## 1. Mục tiêu và Kịch bản
- Nhánh `main` có 2 commit mới liên quan đến thay đổi `port` thành `8081` và thêm `env` là `production`.
- Nhánh `feature-api` cũng có 2 commit sửa `port` thành `9000` và bật `debug` lên `true`.
- Do hai nhánh đều sửa đổi cùng một đoạn ở tệp `config.json`, khi chạy lệnh `git rebase main` từ nhánh `feature-api`, xung đột sẽ nổ ra ở từng chặng commit.

## 2. Giải quyết xung đột từng bước (Step-by-step Conflict Resolution)

### Lần 1: Xung đột tại commit "feat: change port"
Khi bắt đầu rebase, Git thông báo lỗi CONFLICT. Tôi đã kiểm tra file `config.json`:
```json
{
<<<<<<< HEAD
  "port": 8081,
  "debug": false,
  "env": "production"
=======
  "port": 9000,
  "debug": false
>>>>>>> 0e116fa (feat: change port)
}
```
**Cách giải quyết:** Tôi đã chỉnh sửa lại thủ công để giữ lại cấu hình cổng của feature (9000) và giữ luôn môi trường production của main:
```json
{
  "port": 9000,
  "debug": false,
  "env": "production"
}
```
Sau đó tôi lưu file, chạy `git add config.json` và tiếp tục quá trình với `git rebase --continue`.

### Lần 2: Xung đột tại commit "feat: enable debug"
Git ngay lập tức báo xung đột ở trạm dừng thứ hai. Nội dung file lúc này bị kẹp giữa những gì tôi vừa giải quyết và nhánh mới:
```json
{
  "port": 9000,
<<<<<<< HEAD
  "debug": false,
  "env": "production"
=======
  "debug": true
>>>>>>> 4421db7 (feat: enable debug)
}
```
**Cách giải quyết:** Tôi tiếp tục gỡ bỏ các ký hiệu đánh dấu và kết hợp nội dung mong muốn:
```json
{
  "port": 9000,
  "debug": true,
  "env": "production"
}
```
Sau đó tiếp tục add và chạy `git rebase --continue` để hoàn tất toàn bộ tiến trình.

## 3. Lịch sử sau khi hoàn thành Rebase
Khác với merge sẽ sinh ra commit phụ và làm nhánh bị rẽ nhánh cong queo, kết quả lệnh `git log --graph --oneline` sau khi Rebase là một đường thẳng hoàn hảo:

```text
* 67931b6 feat: enable debug
* ca7d8d2 feat: change port
* 05e0c94 add env config
* 36c75f3 update port on main
* d734bfd init config
```

Lịch sử của nhánh `feature-api` đã được bốc lên và nối tiếp một cách gọn gàng, thẳng tắp ngay sau commit mới nhất của nhánh `main`.
