# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Nguyễn Bình Dương<br>
**MSSV:** 2A202602170<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:
"f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33"
- Bốn mã ảnh:
    "drive_022",
    "drive_033",
    "drive_038",
    "drive_008"
- Số vật thể thực tế: 52
- Mã SHA-256 của gói YOLO của bạn:
"e090f46ebdf39fb81f5f1bf38513299ec5b3ebc703e1ce1573472bacae6cad5c"
- Mã SHA-256 của gói CVAT gốc của bạn:
"354d85db74262cc151323792fc76698abddfede2b6547166230b31e31cdfd353"
- Nguồn đối chiếu: bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu:
c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: 16h

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

“Tôi tự gán nhãn và tự kiểm tra trên bốn ảnh trước khi xem bộ tham chiếu. Hai gói YOLO và CVAT được xuất từ bài làm của tôi, sau đó được kiểm tra và dùng để huấn luyện thử. Tôi chỉ tiếp nhận bộ tham chiếu để đối chiếu sau bước này, đồng thời giữ lại kết quả ban đầu để phân biệt quyết định độc lập với các sửa đổi sau đối chiếu.”
## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022, xe lớn màu vàng–xanh ở tiền cảnh | bus | Thân dài, nhiều cửa sổ, cấu trúc xe chở khách | Xe khách thân dài, có nhiều cửa sổ hoặc hàng ghế thuộc lớp bus |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

“bus mô tả loại phương tiện. visibility=occluded mô tả tình trạng bị che khuất. Một xe vẫn thuộc lớp bus dù nhìn rõ hay bị che khuất; tình trạng quan sát được lưu bằng thuộc tính.”

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Bỏ sót xe car ở mép dưới ảnh drive_038 vì xe chỉ xuất hiện một phần trong khung hình | Phạm vi — bỏ sót vật thể | Quan sát ảnh gốc và ảnh phủ, thấy xe có hộp tham chiếu nhưng chưa có hộp của tôi | Bổ sung hộp lớp car, đặt boundary=truncated. Hộp nằm trong biên ảnh và theo quy tắc hình học của bài; không bỏ qua xe chỉ vì bị cắt bởi mép ảnh nếu vẫn đủ dấu hiệu nhận diện |

- Số hộp `needs_review` trước và sau khi kiểm: 1
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: “Với xe ở xa, chưa đủ dấu hiệu để phân biệt car và van, tôi đánh dấu needs_review."

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 2 0.385148 0.729406 0.446422 0.363438
- Tên lớp và tọa độ điểm ảnh `xyxy`: bus, [103.6, 350.5, 389.3, 583.1]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

“Kiểm tra định dạng chỉ xác nhận dòng có năm trường hợp lệ, mã lớp nằm trong danh sách và tọa độ nằm trong giới hạn. Dòng vẫn có thể sai lớp nếu nhận diện nhầm loại xe; sai phạm vi nếu gán một đối tượng không thuộc yêu cầu; hoặc sai hình học nếu hộp cắt mất xe hay bao quá nhiều nền.”

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038.
- Mã ảnh thẩm định: drive_008.
- Mô tả một dự đoán trong `detect_result.jpg`: “Ảnh kết quả không hiển thị hộp hoặc tên lớp dự đoán. Cụ thể, xe buýt vàng–xanh lớn bên phải ảnh cũng không có hộp hiển thị ở ngưỡng confidence 0.25.”
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?
“Cần kiểm tra số lượng và sự đa dạng của nhãn bus trong ba ảnh huấn luyện, tính nhất quán của lớp và hộp, cùng ảnh hưởng của thời gian huấn luyện ngắn và ngưỡng confidence. Chưa thể kết luận nhãn sai chỉ từ việc không có hộp dự đoán.”
- Minh chứng nào có thể bác bỏ nhận định của bạn?
“Nếu rà soát ảnh và nhãn cho thấy lớp cùng hình học đều đúng, còn kiểm tra đầu ra mô hình cho thấy hộp đúng nhưng confidence dưới 0.25, thì giả thuyết lỗi nhãn gây bỏ sót chưa được hỗ trợ.”
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
“Ba ảnh huấn luyện và một ảnh thẩm định quá ít để đại diện cho các điều kiện giao thông, góc nhìn, ánh sáng và che khuất. Ảnh thẩm định còn được dùng để chọn mô hình tốt nhất, nên không phải bộ kiểm thử độc lập. Kết quả chỉ phục vụ chẩn đoán trong bài thực hành.”

## 6. Đối chiếu nhãn

- Số hộp ghép được: 35
- IoU trung bình và trung vị: 0.850069 và 0.852510
- Mức đồng thuận lớp: 62.8571%
- Số hộp phía bạn không ghép được: 17
- Số hộp phía đối chiếu không ghép được: 15
- Một điểm khác biệt cụ thể: “Ở drive_022, hộp xe buýt lớn phía trước của tôi kéo rộng hơn về bên trái và phía dưới so với hộp tham chiếu.”
- Quy tắc hoặc hành động sửa phát sinh: “Rà lại quy tắc xác định biên hộp và kiểm trực tiếp trên ảnh gốc. Ưu tiên kiểm 13 cặp khác lớp và các hộp không ghép được; ghi lý do giữ hoặc sửa từng trường hợp.”
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
Hai bên có thể cùng hiểu sai quy tắc, cùng bỏ sót vật thể hoặc cùng gán nhầm lớp. Đồng thuận lớp chỉ tính trên các cặp đã ghép; IoU cao chỉ thể hiện hộp gần nhau về hình học. Trong bài này, IoU trung bình khoảng 0.85 nhưng đồng thuận lớp chỉ khoảng 62.86%, nên vẫn cần rà soát lớp.”

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
“Minh chứng mạnh nhất về tính nhất quán của hai gói xuất là 52 hộp YOLO ghép được với 52 hộp CVAT, với IoU nhỏ nhất khoảng 0.999957; báo cáo cũng xác nhận đủ ba thuộc tính cho mọi hộp. Kết quả này chứng minh hai gói giữ cùng trạng thái nhãn, chưa chứng minh nhãn đúng về nội dung.
Câu hỏi cho Lab Coach: Với 13 cặp ghép khác lớp và 32 hộp không ghép được tính trên cả hai phía, em nên ưu tiên làm rõ quy tắc phân lớp, phạm vi gán hay hình học ở những trường hợp nào?”
