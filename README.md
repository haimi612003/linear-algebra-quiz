# Ôn tập Đại số tuyến tính

Web ôn tập trắc nghiệm 120 câu Đại số tuyến tính nền tảng — từ scalar đến `Ax = b`. Chọn chương, rút câu ngẫu nhiên, làm bài, xem giải thích ngay sau mỗi câu và thống kê theo chương ở cuối lượt.

Là một trang static thuần: không cần build, không cần `npm install`, không có backend.

## Chạy dự án

Phải chạy qua HTTP server — **không** mở file HTML bằng cách double-click, vì trang dùng `import('./quiz-data.js')` và giao thức `file://` chặn ES module (bộ câu hỏi sẽ không load, header đứng ở "đang tải…").

```bash
cd "Web ôn tập trắc nghiệm"
python3 -m http.server 8765 --bind 127.0.0.1
```

Rồi mở: http://127.0.0.1:8765/Ôn%20tập%20Đại%20số%20tuyến%20tính.dc.html

Cách khác: extension **Live Server** của VS Code — bấm phải file HTML → *Open with Live Server*.

Trang nạp font từ Google Fonts nên lần đầu cần mạng; mất mạng thì vẫn chạy, chỉ đổi sang font hệ thống.

## Tính năng

**Trang chọn đề**
- Chọn 1 hoặc nhiều chương trong 7 chương; bỏ chọn hết = ôn toàn bộ 120 câu.
- Số câu mỗi lượt: 10 / 20 / 30 / 50 / tất cả.
- Bật/tắt xáo trộn thứ tự đáp án.
- *Tiếp tục lượt đang dở* — hiện ra khi có lượt chưa làm xong đã lưu.
- *Ôn lại N câu từng sai* — hiện ra khi đã tích lũy câu sai.

**Khi làm bài**
- Chấm ngay sau khi chọn: đáp án đúng tô xanh, đáp án chọn sai tô đỏ, kèm giải thích và tham chiếu mục lý thuyết.
- Thanh tiến độ + đếm số câu đúng/sai.
- Phím tắt: `1`–`4` chọn đáp án, `Enter` (hoặc `Space`) sang câu tiếp.
- *Kết thúc sớm* để chốt điểm giữa lượt.

**Trang kết quả**
- Điểm số và phần trăm chính xác.
- Biểu đồ thanh theo từng chương để thấy chương nào yếu.
- Danh sách các câu làm sai: câu hỏi, đáp án đã chọn, đáp án đúng, giải thích.
- Làm lượt mới, làm lại riêng các câu sai, hoặc về trang chọn đề.

## Cấu trúc file

| File | Vai trò |
| --- | --- |
| `Ôn tập Đại số tuyến tính.dc.html` | Toàn bộ giao diện + logic quiz (class `Component extends DCLogic` trong `<script type="text/x-dc">`) |
| `quiz-data.js` | Bộ 120 câu hỏi và 7 chương — ES module, export `QUESTIONS` và `CHAPTERS` |
| `support.js` | Runtime dựng sẵn (React + template `x-dc`). **File generated — không sửa tay** |
| `uploads/quiz-raw.txt` | Bản text gốc của bộ câu hỏi (nguồn để soạn `quiz-data.js`) |
| `uploads/linear-algebra-quiz.pdf` | Bản PDF gốc |

### 7 chương

| # | Tên chương | Câu |
| --- | --- | --- |
| 1 | Scalar, Variable và Constant | 1–16 |
| 2 | Vector | 17–34 |
| 3 | Matrix | 35–50 |
| 4 | Tensor | 51–60 |
| 5 | Các phép toán trên vector và ma trận | 61–82 |
| 6 | Bản chất ma trận: Hệ phương trình tuyến tính | 83–102 |
| 7 | Phép nhân ma trận với vector | 103–120 |

## Thêm hoặc sửa câu hỏi

Sửa mảng `raw` trong `quiz-data.js`. Mỗi câu là một mảng 6 phần tử:

```js
[id, chương, "nội dung câu hỏi", ["đáp án A", "B", "C", "D"], chỉ_số_đáp_án_đúng, "giải thích"]
```

- `chỉ_số_đáp_án_đúng` đếm từ **0** — `0` là đáp án đầu tiên trong mảng.
- Câu Đúng/Sai: truyền hằng `TF` thay cho mảng đáp án, rồi `0` = Đúng, `1` = Sai.

```js
[101,6,"(Đúng/Sai) Ax = b chỉ là một cách viết gọn…",TF,0,"Đúng — …"],
```

- `id` cần duy nhất, vì danh sách câu từng sai được lưu theo `id`.
- Muốn thêm chương mới thì thêm một entry vào `CHAPTERS` (`id`, `name`, `range`).

Sửa xong chỉ cần refresh trang, không phải build lại gì.

## Dữ liệu lưu ở đâu

Trong `localStorage` của browser, dưới hai khóa:

- `lat-quiz-v1` — lượt đang làm dở (danh sách câu, vị trí, kết quả). Tự xóa khi làm xong lượt.
- `lat-quiz-wrong-v1` — mảng `id` các câu từng làm sai, tích lũy qua nhiều lượt.

Không có server nên tiến độ chỉ nằm trên máy và browser đang dùng. Muốn reset sạch, mở DevTools console và chạy:

```js
localStorage.removeItem('lat-quiz-v1');
localStorage.removeItem('lat-quiz-wrong-v1');
```
