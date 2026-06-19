# Reflection — Day 17 (≤ 200 words)

Answer briefly, in your own words. This is graded on reasoning, not length.

1. **The flywheel.** Bước `decontaminate` dễ bị vỡ ngầm nhất (silently break). Nếu so sánh chuỗi bị lệch khoảng trắng/casing hoặc lỗi logic lọc, cặp DPO bẩn vẫn được sinh ra và đưa vào train. Ta phát hiện bằng cách thêm kiểm tra tích hợp (assert `len(set(eval_prompts) & set(train_prompts)) == 0`) trước khi bắt đầu train.

2. **Decontamination.** Nếu bỏ qua, model sẽ ghi nhớ (overfit) trực tiếp các câu hỏi trong tập eval. Chỉ số eval (như Win Rate, ROUGE) sẽ tăng vọt ảo (gần 100%), tạo ra một "lời nói dối ngọt ngào", nhưng model thực tế trong production sẽ suy giảm chất lượng rõ rệt đối với các truy vấn mới của người dùng.

3. **Point-in-time.** Feature `account_balance` (số dư tài khoản) hoặc `transaction_count_last_24h` trong hệ thống phát hiện gian lận ngân hàng. Nếu không dùng ASOF join, mô hình khi train sẽ thấy số dư sau khi đã thực hiện giao dịch gian lận (thường bằng 0), gây rò rỉ tương lai và làm liệt khả năng dự đoán real-time.

4. **Graph vs vector.**
* **KG tốt hơn:** Câu hỏi multi-hop như *"Sản phẩm có hạn bảo hành dài nhất được sản xuất ở nhà máy nào?"* (Sản phẩm -> Hạn bảo hành -> Nhà máy).
* **KG overkill:** Câu hỏi tìm kiếm đơn giản *"Hạn bảo hành của Widget là bao nhiêu?"* (Vector search trích xuất trực tiếp 1 chunk tài liệu là đủ, nhanh và rẻ hơn nhiều).
