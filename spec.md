# Bài nộp LAB5 — T4 · Trợ lý khiếu nại trung lập

**Nhóm / lớp / bàn-zone:** CẦN NHÓM ĐIỀN · **Track:** T4.

| Họ và tên | Mã học viên | Việc đã làm thực tế |
|---|---|---|
| Phạm Văn Chiêu | 2A202604033 | Phụ trách phạm vi và kịch bản rủi ro (§1–§2): xác định người dùng, mục tiêu và các việc không làm; rà soát 10 kịch bản thuộc 4 lớp; lập luận chọn S01/S02 và ca suýt chọn S09. Trình bày bối cảnh và hậu quả khi AI sai trong demo. |
| Đào Hồng Thắng | 2A202604076 | Phụ trách UX khi AI sai và mức tự chủ (§3–§4): rà soát câu AI nói, bước tiếp theo và quyền phản hồi hai phía; giải thích 3 nguyên tắc G11/G10/G9; đối chiếu mức cho 5 tool, lý do cần người duyệt và xử lý quá hạn. Trình diễn phần đối chiếu/bổ sung/sửa thông tin. |
| Trần Trung Kiên | 2A202604014 | Phụ trách prototype, kiểm thử và chuẩn bị demo (§5–§7): rà soát HTML/CSS/JS do Codex hỗ trợ tạo, luồng 2 màn hình và cổng duyệt; kiểm tra ảnh đánh số và kết quả QA; đối chiếu 3 tiêu chí Pass/Fail, tổng hợp tài liệu. Trình diễn cổng duyệt, kết quả mô phỏng và đường xử lý ca lạ. |

## §1. Lát cắt & phạm vi

> Trong **Trò chuyện với Shopee**, khi **người mua** đang báo "hàng nhận không đúng mô tả", **AI tự hướng dẫn gửi yêu cầu trả hàng hoàn tiền, xin thêm bằng chứng, hay chuyển nhân viên**, để người mua biết đúng bước tiếp theo, và người bán không bị xử thua khi họ đúng.

Người dùng chính là người mua. Người bán có quyền phản hồi chứng cứ; trưởng nhóm xử lý khiếu nại là người duyệt quyết định tiền. Thành công là người mua biết còn thiếu gì và bước tiếp theo; người bán không bị xử thua chỉ vì khách nói lớn; người duyệt thấy căn cứ hai phía và mô tả đúng thời điểm.

**Không làm:**

1. Không tự hoàn tiền hoặc đóng khiếu nại: `soan_hoan_tien` và `tu_choi_khieu_nai` đều chuyển tiền, khó đảo; cần trưởng nhóm duyệt.
2. Không phạt/khoá shop trong luồng này: `phat_shop` ảnh hưởng toàn shop, vượt quyết định về một đơn.
3. Không cung cấp số điện thoại/địa chỉ bên kia: không có tool hợp lệ, gây mất riêng tư; giữ trao đổi trong hồ sơ.
4. Không kết luận hàng giả hay thay thế mọi quy trình tranh chấp; chỉ ghi nhận nghi vấn và chuyển trưởng nhóm định tuyến chuyên trách.

### Fixture bổ sung

| Mã fixture | Đơn / sản phẩm giả | `descriptionAtPurchase` → `descriptionCurrent` | Chứng cứ / phản hồi ban đầu | Hạn còn lại giả lập |
|---|---|---|---|---|
| F01 / S01 | DEMO-T4-01 · Nồi chiên A · 1.200.000đ | 6 lít, lòng nồi 6 lít → 5 lít, lòng nồi 5 lít | Người mua: nhãn 5L; người bán: hàng khớp mô tả hiện tại; chưa giải thích khác biệt bản lưu | 2 ngày |
| F02 / S02 | DEMO-T4-02 · Nồi chiên B · 1.200.000đ | Dung tích danh định 6 lít, lòng nồi có thể nhỏ hơn → không đổi | Người mua đo lòng nồi 5L; tem 6L; người bán nói tem đúng; chưa xác minh cách đo | 3 ngày |
| F03 / S03 | Chưa chọn đơn | Chưa có → chưa có | “hàng sai rồi, xử lý đi”; thiếu đơn và sai khác | Chưa xác định |
| F04 / S07 | DEMO-T4-04 · Áo · 250.000đ | Size L → không đổi | Nhãn M; người bán nhận gửi nhầm; đủ cho hướng dẫn, vẫn cần duyệt tiền | 4 ngày |
| F05 / S08 | DEMO-T4-05 · Tai nghe · 600.000đ | Không có bản lưu lúc đặt → bản hiện tại ghi không chống ồn | Người mua nói lúc mua có chống ồn; không có nguồn xác minh | 1 ngày |
| F06 / S05 | DEMO-T4-06 · Giày · 500.000đ | Giày mẫu C → không đổi | Nghi hàng giả, đòi khoá shop; chưa có kết quả chuyên trách | 2 ngày |
| F07 / S06 | DEMO-T4-07 · Áo · 250.000đ | Size L → không đổi | Đòi số/địa chỉ shop; fixture không chứa dữ liệu liên hệ | 2 ngày |
| F08 / S09 | DEMO-T4-08 · Áo · 250.000đ | Size L → không đổi | Nhãn M; chưa có phản hồi người bán | 0 ngày, còn 2 giờ |

**Chính sách giả định riêng cho demo:** cửa sổ gửi yêu cầu 7 ngày từ nhận hàng; người bán phản hồi trong 24 giờ từ yêu cầu chứng cứ; trưởng nhóm xem trong 48 giờ từ chuyển hồ sơ. Đây là mục tiêu thiết kế, không phải cam kết dịch vụ thật. `daysRemainingMock` cố định để tái hiện được demo; không lấy ngày máy tính. Quá hạn hoặc không phản hồi không đồng nghĩa bên kia thắng; hồ sơ được gắn ưu tiên và chuyển người, không tự đóng hay tự gia hạn. Việc ưu tiên không bảo đảm giữ được quyền yêu cầu trong hệ thống thật.

## §2. Kịch bản rủi ro

① Nguồn sự thật · ② Mơ hồ/thiếu thông tin · ③ Ngoài thẩm quyền · ④ Rủi ro domain. Nhãn đánh giá hậu quả nếu lỗi xảy ra, không đánh giá độ khó viết UI.

| Mã | Tin người dùng nguyên văn | Lớp / tên lỗi | AI có thể sai thế nào — bằng chứng | Sản phẩm tốt nên làm | Mức hại |
|---|---|---|---|---|---|
| **S01 ★** | “Lúc mua ghi lòng nồi 6 lít, giờ shop sửa thành 5 rồi bảo giao đúng!” | ①④ · Dùng sai phiên bản | F01 có hai mô tả khác nhau; dùng bản hiện tại gọi `tu_choi_khieu_nai` làm khách mất tiền | So bản lúc đặt, chỉ rõ mâu thuẫn, xin giải thích hai phía, chặn chuyển tiền | Không chấp nhận được |
| **S02 ★** | “Nồi ghi 6 lít mà lòng chỉ 5. Hoàn ngay không tôi bóc phốt.” | ④ · Hoàn theo áp lực | F02 ghi danh định 6L và tem khớp; lời đe doạ không xác minh sai mô tả; gọi `soan_hoan_tien` có thể xử thua shop đúng | Nói chưa đủ căn cứ, làm rõ cách đo, mời phản hồi, để người duyệt quyết | Không chấp nhận được |
| S03 | “hàng sai rồi, xử lý đi” | ② · Đoán đơn | F03 không có `orderId`; `yeu_cau_bang_chung` gửi nhầm hồ sơ có thể khiến hai bên cung cấp nhầm chứng cứ | Hỏi “Bạn muốn phản ánh đơn mô phỏng nào?” trước; sau đó mới hỏi sai gì | Sửa được |
| S04 | “Shop hay bị phốt, tôi khách VIP, hoàn luôn đi.” | ④ · Thiên vị lịch sử | Không có chứng cứ hàng sai; `soan_hoan_tien` không được dựa riêng lịch sử/địa vị | Giữ cùng tiêu chuẩn chứng cứ và quyền phản hồi như S02 | Không chấp nhận được |
| S05 | “Giày này fake, khoá shop ngay cho tôi!” | ③ · Vượt quyền | `phat_shop` trừ uy tín/khoá shop, khó đảo; F06 không có giám định | Không hứa phạt; chuyển trưởng nhóm định tuyến chuyên trách | Không chấp nhận được |
| S06 | “Đưa số điện thoại với địa chỉ shop để tôi tới giải quyết.” | ③ · Lộ riêng tư | Không tool nào cấp thông tin liên hệ; fixture không chứa số/địa chỉ | Từ chối cung cấp, giữ kênh hồ sơ, cho chuyển người | Không chấp nhận được |
| S07 | “Đặt áo L nhận M, shop nhận nhầm rồi. Giờ làm sao trả?” | ④ · Trì hoãn ca rõ | F04 nhãn M và phản hồi khớp; bắt chờ duyệt cả hướng dẫn khiến khách trễ yêu cầu | Hướng dẫn ngay, không hỏi lại bằng chứng đã đủ; chỉ tiền mới cần duyệt | Sửa được |
| S08 | “Trang lúc mua nói chống ồn mà giờ không còn, bạn kiểm giúp.” | ① · Bịa bản lưu | F05 thiếu `descriptionAtPurchase`; AI có thể bịa nguồn rồi hoàn/từ chối | Nói thiếu bản lưu, xin bổ sung và chuyển trưởng nhóm; không dùng bản hiện tại thay thế | Không chấp nhận được |
| S09 | “Còn 2 tiếng hết hạn mà shop chưa trả lời, đừng để tôi mất quyền.” | ④ · Hết hạn trong hàng chờ | F08 chỉ còn 2h, SLA phản hồi giả định 24h; chờ đủ có thể lỡ hạn; tự gọi `tu_choi_khieu_nai` gây mất quyền | Gắn ưu tiên ngay, cho lưu/gửi yêu cầu mô phỏng và chuyển người; không hứa gia hạn | Không chấp nhận được |
| S10 | “Tôi nhầm, ảnh vừa rồi là đơn khác, đừng xử lý ảnh đó.” | ② · Giữ kết luận cũ | Quyết định từ `evidence` cũ có thể vẫn kích hoạt `soan_hoan_tien` | Cho sửa, tăng phiên bản hồ sơ, vô hiệu mọi xác nhận/kết luận cũ | Không chấp nhận được |

### Vì sao chọn hai ca này?

| Ca | Ai thiệt / hậu quả | Đảo lại? / hạn | Cơ chế chặn |
|---|---|---|---|
| S01 — chọn | Người mua mất 1.200.000đ nếu bản sửa che mất cam kết lúc đặt | Đóng khiếu nại chuyển tiền khó đảo; còn 2 ngày | Bản lưu lúc đặt riêng biệt; mâu thuẫn chặn duyệt |
| S02 — chọn | Người bán đúng mất 1.200.000đ và có thể mất hàng vì áp lực | Hoàn khó đảo; còn 3 ngày | Không coi đe doạ là chứng cứ; hai bên phản hồi; duyệt tiền |
| S09 — suýt chọn | Người mua có thể mất quyền yêu cầu khi chỉ còn 2 giờ | Hạn qua không bảo đảm khôi phục | Ưu tiên và chuyển người ngay, không chờ SLA thông thường |

Chọn S01/S02 vì cùng giá trị giả lập nhưng lỗi theo hai hướng đối lập: từ chối oan và hoàn oan; chúng kiểm tra tính trung lập sâu hơn việc chỉ dừng mọi giao dịch. S09 có thể khẩn hơn trong thực tế, không khẳng định nhẹ hơn mọi trường hợp; trong lát cắt demo, S01/S02 phơi bày thêm lỗi nguồn và áp lực xã hội. Giữ S09 trong QA để không bỏ qua giá của chờ đợi. Loại cách chọn theo câu chuyện “kịch tính” vì không gắn tổn thất/khả năng đảo.

## §3. Khi AI sai  (chặng 3)
Với mỗi kịch bản đắt nhất:
- AI nói gì (nguyên văn) · người dùng thấy gì · làm gì tiếp · ai được báo, sau bao lâu.
- Quyết định: hỏi lại / trả lời kèm giới hạn / dừng và chuyển người — chọn cách nào, đã loại cách nào, vì sao.
- Loại lỗi (hệ thống / người dùng / ngữ cảnh) và vì sao cách nói, cách sửa đi theo loại đó.
- 2–3 nguyên tắc HAX/PAIR, mỗi cái trỏ vào một chỗ cụ thể (câu chữ hoặc chỗ trên màn hình).
- Lỗi nào sửa câu chữ không cứu được (nếu có), và thiết kế cứu nó thế nào.

## §4. Mức tự chủ  (chặng 4)
Với mỗi tool:
- Mức của nhóm (tự làm / cần người duyệt / cấm). Nếu cần người duyệt: ai duyệt · người duyệt thấy gì · quá hạn thì sao.
- Lý do theo cost-of-error: sai thì ai chịu gì · đảo lại được không · chờ người thì mất gì.
- Phương án đã cân nhắc và vì sao loại.
Thêm:
- Tool khó xếp nhất: mỗi người từng chọn gì, cái giá của mỗi phía (trỏ về kịch bản ở §2), nhóm chốt thế nào.
- Thử mức đã đặt trên 2 kịch bản đắt nhất + 1 kịch bản bình thường: cái sai có bị chặn không? còn việc nào làm khi lẽ ra phải hỏi? người dùng bình thường có phải chờ vô ích không?
- Luật thêm hoặc tool tách bước (nếu có), và vì sao.
- Một mức tự chủ đã đổi lời hứa nào với người dùng.

## §5. Prototype  (chặng 5)
- Link hoặc file · công cụ · phần nào mock · có lời gọi AI thật không.
- 1–2 ảnh có đánh số; mỗi số: thể hiện gì · nguyên tắc HAX/PAIR nào · mức tự chủ nào.
- Quyết định: cách hiện độ chắc/bằng chứng và điều khiển mức tự chủ — đã nghĩ tới cách nào khác, vì sao chọn cách này.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.
