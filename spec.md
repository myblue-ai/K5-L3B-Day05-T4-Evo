# Bài nộp LAB5 — T4 · Trợ lý khiếu nại trung lập

**Nhóm / lớp / bàn-zone:** Evo · L3B · **Track:** T4.

| Họ và tên | Mã học viên | Việc đã làm thực tế |
|---|---|---|
| Phạm Văn Chiêu | 2A202604033 | Phụ trách phạm vi, kịch bản rủi ro và mức tự chủ (Product & Risk - §1, §2, §4): xác định người dùng, mục tiêu và các việc không làm; rà soát 10 kịch bản thuộc 4 lớp; lập luận chọn S01/S02 và ca suýt chọn S09; phân bổ mức tự chủ 5 tool theo Cost of Error, cơ chế cổng duyệt và ca khó xếp nhất soan_hoan_tien. Trình bày bối cảnh và hậu quả khi AI sai trong demo. |
| [Chưa cập nhật] | [Chưa cập nhật] | [Thành viên 2 - AI UX & Content: chưa có / để nguyên theo template] |
| [Chưa cập nhật] | [Chưa cập nhật] | [Thành viên 3 - Prototype & QA: chưa có / để nguyên theo template] |

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
*(Phần việc của Người 2 — AI UX & Content: để nguyên theo template)*

Với mỗi kịch bản đắt nhất:
- AI nói gì (nguyên văn) · người dùng thấy gì · làm gì tiếp · ai được báo, sau bao lâu.
- Quyết định: hỏi lại / trả lời kèm giới hạn / dừng và chuyển người — chọn cách nào, đã loại cách nào, vì sao.
- Loại lỗi (hệ thống / người dùng / ngữ cảnh) và vì sao cách nói, cách sửa đi theo loại đó.
- 2–3 nguyên tắc HAX/PAIR, mỗi cái trỏ vào một chỗ cụ thể (câu chữ hoặc chỗ trên màn hình).
- Lỗi nào sửa câu chữ không cứu được (nếu có), và thiết kế cứu nó thế nào.

## §4. Mức tự chủ  (chặng 4)

| Tool gốc / tác động | Mức đề xuất / điều kiện | Sai thì ai chịu gì; đảo được không? | Chờ người mất gì? | Phương án đã loại |
|---|---|---|---|---|
| `yeu_cau_bang_chung` — gửi mẫu | **Tự làm** khi biết đơn và phần thiếu; tối đa 1 yêu cầu/bên/phiên bản; không hỏi lại thứ đã có | Hai bên tốn công hoặc bị lộ dữ liệu nếu mẫu quá rộng; tin đã tới không thu hồi. Chỉ xin bằng chứng về hàng, không số/địa chỉ | S09 có thể hết hạn chỉ vì chờ duyệt tin | Cần duyệt mọi tin làm chậm bước rẻ; tự gửi lặp vô hạn tạo áp lực. Cho sửa yêu cầu tiếp theo, không hứa unsend |
| `chuyen_truong_nhom` — chuyển hồ sơ | **Tự làm** theo yêu cầu hoặc quá hạn; đính kèm căn cứ hiện có và chỗ thiếu | Chuyển nhầm làm khách chờ, nhân viên quá tải; có thể huỷ chuyển nếu chưa quyết định | Chờ duyệt chuyển người khiến ca mắc kẹt; S01/S09 càng chậm | Cấm chuyển để tiết kiệm nhân lực bỏ rơi ca khó; cần duyệt trước khi chuyển tạo vòng chờ |
| `soan_hoan_tien` — **hoàn tiền**, không phải soạn nháp | **Cần người duyệt**: trưởng nhóm xử lý khiếu nại | S02: shop mất tiền/hàng, khó đảo; không có undo tiền trong UI | S07 khách chờ nhận tiền, S09 có nguy cơ trễ; giảm chờ bằng hướng dẫn ngay và ưu tiên, không bỏ cổng duyệt | Tự làm dễ hoàn oan; cấm tuyệt đối làm khách có căn cứ không được giải quyết |
| `tu_choi_khieu_nai` — đóng, tiền về shop | **Cần người duyệt**: trưởng nhóm xử lý khiếu nại | S01: người mua mất quyền/tiền dù bản lúc đặt có lợi, khó đảo | Shop đúng S02 phải chờ tiền, khách chờ kết luận | Tự làm thiên về shop; cấm tuyệt đối khiến khiếu nại không căn cứ không có kết thúc |
| `phat_shop` — trừ uy tín/khoá shop | **Cấm** trong lát cắt | S05: ảnh hưởng cả shop và các đơn khác, khó đảo, một xác nhận đơn lẻ không đủ | Chờ quy trình chuyên trách có thể chậm bảo vệ người khác; báo trưởng nhóm định tuyến, không hứa xử phạt | Cho AI tự/cần người duyệt trong luồng thường vẫn vượt phạm vi; không thêm nút phạt |

**Cổng duyệt cho hai tool tiền:** người duyệt là trưởng nhóm, không phải người mua bấm đồng ý. Trên màn hồ sơ, họ thấy đơn, số tiền, bản lúc đặt/hiện tại, phản hồi hai phía, trạng thái thiếu/mâu thuẫn, phiên bản, hạn, nhật ký. Phải ghi nhận đã đọc và chọn quyết định rồi bấm xác nhận riêng. Nếu thiếu/mâu thuẫn/đã sửa mà chưa xác minh lại thì chặn, dù checkbox được tick. Mỗi hồ sơ chỉ có một kết quả mô phỏng; không thể vừa hoàn vừa từ chối. Chuyển tab vai trò chỉ phục vụ demo, không phải xác thực nhân viên.

**Quá hạn 24h phản hồi / 48h xem xét giả định:** chuyển hoặc ưu tiên trưởng nhóm ngay; không tự thực thi tiền, không tự xử thua bên im lặng. Nút “Giả lập quá hạn” tăng trạng thái ưu tiên, không thay đổi timestamp thực hay tuyên bố gia hạn. Trưởng nhóm phải bổ sung/xác minh ngoài demo khi fixture chưa đủ.

### Tool khó xếp nhất: `soan_hoan_tien`

| Thành viên | Mức chọn độc lập | Lý do / ca minh chứng |
|---|---|---|
| Phạm Văn Chiêu | Tự làm khi chứng cứ rõ | Ưu tiên giảm thời gian chờ cho người mua: ở S07, nhãn áo M khác size L đã đặt và shop nhận gửi nhầm, nên hoàn sớm giúp khách không phải chờ thêm. Điểm yếu là AI có thể đánh giá nhầm thế nào là “đủ rõ”; S02 cho thấy lời khiếu nại có vẻ thuyết phục vẫn có thể dẫn tới hoàn oan. |
| Đào Hồng Thắng | Cần người duyệt | Hoàn tiền sai khiến người bán mất tiền và có thể mất hàng, khó đảo ngược. Ở S02, lời đe doạ không chứng minh hàng sai; trưởng nhóm cần xem bản lúc đặt và phản hồi hai phía trước khi duyệt. Chấp nhận chờ ở bước tiền, nhưng cho AI hướng dẫn và thu thập chứng cứ ngay. |
| Trần Trung Kiên | Cần người duyệt | Ưu tiên tránh thiệt hại tài chính khó khắc phục: AI có thể tin lời một phía hoặc dùng nhầm phiên bản mô tả như S01/S02. Cấm tuyệt đối, tuy cũng tránh được thiệt hại, nhưng lại có điểm yếu là khiến khách có căn cứ như S07 không được giải quyết tới cùng, phải chuyển sang một quy trình khác và có thể chậm hơn. |

**Cách nhóm chốt thực tế: cần người duyệt** — mức chốt theo xác nhận của người dùng. Căn cứ lựa chọn là hậu quả của hoàn tiền sai khó đảo ngược, trong khi cấm hoàn tiền hoàn toàn lại làm chậm việc giải quyết các ca có căn cứ. AI được đối chiếu, hướng dẫn và xin bổ sung chứng cứ; `soan_hoan_tien` chỉ được thực hiện sau khi trưởng nhóm xử lý khiếu nại xem bản mô tả lúc đặt, chứng cứ và phản hồi hai phía, rồi xác nhận quyết định. Người mua bấm đồng ý không thay thế quyền duyệt của trưởng nhóm.

Nhóm loại phương án tự hoàn vì S02 có thể khiến shop đúng mất tiền; loại phương án cấm tuyệt đối vì S01/S07 vẫn cần một đường hoàn tiền hợp lệ. Để giảm cái giá của chờ duyệt, AI hướng dẫn ngay ở S07 và gắn ưu tiên/chuyển trưởng nhóm ở S09. Nếu quá hạn chưa có người duyệt, không tự hoàn, không tự đóng khiếu nại và không coi im lặng là đồng ý. Quyết định này khớp cổng duyệt đang có trên prototype; ba ý kiến mẫu phía trên cần từng thành viên xác nhận hoặc sửa theo lựa chọn độc lập của mình.

### Thử mức trên 3 ca

| Ca | Cái sai bị chặn? | Có làm khi lẽ ra phải hỏi? | Có bắt chờ vô ích? |
|---|---|---|---|
| S01 | Mâu thuẫn bản mô tả khoá cả hai tool tiền; không tự từ chối | Yêu cầu bổ sung về đúng đơn; không đoán bản hiện tại là hợp đồng | Thu thập/chuyển làm ngay; chỉ tiền chờ người |
| S02 | Tem khớp không tự hoàn và không tự bác; đe doạ không đổi mức | Chờ làm rõ phép đo và quyền phản hồi | Người mua có bước bổ sung ngay; duyệt tiền cần thiết vì có bên mất tiền |
| S07 | Hướng dẫn ngay; tiền chỉ sau duyệt | Không xin lại ảnh/nhận lỗi đã có | Hướng dẫn không chờ; nhận tiền vẫn chờ người, đây là chi phí có chủ đích |

**Lời hứa thay đổi:** thay “AI tự giải quyết và hoàn ngay” bằng “Trợ lý giúp đối chiếu và chuẩn bị bước tiếp theo; quyết định chuyển tiền do trưởng nhóm duyệt.” Đây là giới hạn quyền, không phải lời hứa AI luôn đúng.

## §5. Prototype  (chặng 5)
*(Phần việc của Người 3 — Prototype & QA: để nguyên theo template)*

- Link hoặc file · công cụ · phần nào mock · có lời gọi AI thật không.
- 1–2 ảnh có đánh số; mỗi số: thể hiện gì · nguyên tắc HAX/PAIR nào · mức tự chủ nào.
- Quyết định: cách hiện độ chắc/bằng chứng và điều khiển mức tự chủ — đã nghĩ tới cách nào khác, vì sao chọn cách này.

## §6. Tốt là gì  (chặng 6 — nhẹ, bỏ được nếu thiếu giờ)
*(Chưa cập nhật / để nguyên theo template)*

- 3 tiêu chí Pass/Fail chấm được; thiết kế của nhóm đạt tiêu chí nào trên 2 kịch bản đắt nhất; lỗi còn lại.

## §7. Demo
*(Chưa cập nhật / để nguyên theo template)*

- Ca lạ nhận được ở demo: prototype đã làm gì, và nhóm có đổi ý gì sau ca đó không.
