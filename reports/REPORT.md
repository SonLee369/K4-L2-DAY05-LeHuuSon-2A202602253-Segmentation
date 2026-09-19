# Báo cáo Day 5 

- Mã học viên theo lớp: 2A202602253
- Ngày / CVAT local: 2026-09-19 / CVAT local (localhost)
- Công cụ đã dùng: Polygon / gợi ý tự động (Mask2Former Panoptic + Semantic)

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, xe ô tô (car) đậu ở nửa phải giữa ảnh, nhìn thấy cả đầu và đuôi xe.
- Class và quy tắc tôi dùng để chọn biên: Class `car`. Tôi vẽ biên theo phần thân xe nhìn thấy, bao gồm kính chắn gió, gương chiếu hậu, bánh xe. Dừng mask tại mép xe chạm đường, không vẽ xuống dưới mặt đường. Phần bóng xe không được tô vì bóng không phải vật thể.
- Nếu dùng gợi ý sau đó: Gợi ý tự động (Mask2Former) tạo polygon bao toàn thân xe và kính chính xác. Em giữ nguyên phần thân xe vì khớp với biên mắt thường nhìn thấy; chỉnh lại phần bánh xe phía dưới bị gợi ý cắt hụt so với bánh thật, kéo biên xuống thêm để bao trọn bánh.

## 3. Một lỗi Em tìm thấy và sửa

- Task/ảnh/vùng: `cp2_slice`, ảnh `000000017627.jpg`, hai xe ô tô đứng sát nhau ở giữa ảnh.
- Lỗi thuộc loại: gộp-tách, hai instance bị gộp thành một mask.
- Bằng chứng em nhìn thấy: Gợi ý tự động tạo một polygon duy nhất bao cả hai xe, không có đường tách ở khe giữa hai xe. Nhìn kỹ thấy khe hẹp tối giữa hai xe cho thấy đây là hai vật riêng biệt.
- Quy tắc và hành động sửa: Mỗi instance riêng biệt phải có mask riêng. Em xóa polygon gộp, vẽ lại hai polygon riêng, một cho xe bên trái, một cho xe bên phải, theo đường viền của từng xe đến khe giữa. Save lại trong CVAT.
- Sau sửa đã Save và export lại chưa? <br>
    * Đã Save và export lại theo format COCO 1.0.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Đã chạy notebook tự kiểm trên Google Colab. Kết quả cho thấy các task được ghi nhận đủ annotation. Điểm tự đánh giá chưa có vì chưa nhận ground truth. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp4_curb` — `7d83710e-4697c3b2.jpg`, vùng bó vỉa giữa mặt đường và vỉa hè | (1) tô bó vỉa vào `road` vì màu sẫm giống nhựa đường; (2) tô vào `sidewalk` vì bó vỉa nằm ở mép vỉa hè và nâng cao hơn mặt đường | Chức năng phân làn: bó vỉa là ranh giới vật lý nâng cao ngăn xe lên vỉa hè, thuộc về vùng vỉa hè về mặt hạ tầng | Em chọn tô bó vỉa vào `sidewalk`. Xin Lab coach xác nhận ranh đúng tại chỗ màu sắc bó vỉa giống mặt đường. |
| `cp1_holes` — `000000144300.jpg`, kính chắn gió và cửa sổ xe | (1) khoét lỗ bên trong mask tại vùng kính vì nhìn thấu qua; (2) tô toàn thân xe kể cả kính vì kính là bộ phận của xe | Quy tắc cp1_holes: mask không được có lỗ , kính/khe nằm trong mask vật theo quy tắc task | Em tô kín cả vùng kính vào mask xe (không khoét lỗ), đảm bảo polygon solid không có lỗ. |
| `hard_panoptic` — `000000460147.jpg`, người đứng bị xe che một phần thân dưới | (1) chỉ vẽ mask phần người nhìn thấy phía trên xe; (2) ước tính và vẽ cả phần thân dưới bị che, coi là một instance đầy đủ | Quy tắc instance: biên theo phần nhìn thấy, không đoán phần bị che khuất hoàn toàn | Em chỉ vẽ phần người nhìn thấy (từ hông trở lên), dừng mask tại mép xe. Phần bị che không vẽ vào. Xin Lab coach xác nhận cách xử lý này đúng với rubric cp5_occlusion. |
