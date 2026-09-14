# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Bình Dương<br>
**MSSV:** 2A202602170<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: drive_008, xe trắng thân hộp ở giữa ảnh, phía dưới xe tải — mã CVAT: 4.
- Dấu hiệu nhìn thấy: Xe có thân hộp nhỏ, kín, có cửa kính; thân không dài như xe buýt.
- Quy tắc áp dụng: Xe thân hộp nhỏ, kín, dùng chở người hoặc hàng thuộc lớp van; không phân lớp theo công dụng suy đoán.
- Quyết định: Gán van — mã lớp 3
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng ảnh kiểm tra hình dáng thân xe. Nếu quyết định còn cần xác minh, đặt needs_review, nếu không thể phân lớp có căn cứ thì không đoán, ghi lý do vào nhật ký.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_038, xe công vụ màu trắng ở giữa, nửa dưới ảnh — mã CVAT: 41.
- Dấu hiệu nhìn thấy: Có cabin phía trước, sàn và thiết bị nâng/kéo phía sau; không phải thân kín một khối.
- Quy tắc áp dụng: Phương tiện có sàn hàng hoặc thiết bị công vụ rõ ràng thuộc lớp truck.
- Quyết định: Gán truck — mã lớp 1.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Kiểm tra phần phía sau cabin; nếu chưa xác định được cấu trúc, đánh dấu needs_review, không suy đoán chỉ từ kích thước.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_033 — Object ID: 22.
- Dấu hiệu nhìn thấy khi phóng 100%: Thấy phần nóc và thân xe dài, dãy cửa sổ bên hông; xe bị cắt ở mép trái và mép dưới ảnh. (Kiểm tra lại ở mức 100% trước khi xác nhận.)
- Giá trị `visibility`: clear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: confident
- Lý do: Phần xe nhìn thấy đủ rõ để xác định lớp bus. Xe bị mép ảnh cắt, không phải bị phương tiện khác che khuất, nên chọn boundary=truncated và visibility=clear. Vẽ hộp sát phần xe nhìn thấy, không ước lượng phần nằm ngoài ảnh.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 52