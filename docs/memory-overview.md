# Memory: Tổng quan

> Các file liên quan: [RAG](rag-overview.md) · [Skill](skill-overview.md) · [MCP](mcp-overview.md) · [So sánh và mối quan hệ](so-sanh-moi-quan-he.md)

## 1. Memory là gì?

**Memory** là khả năng agent **lưu giữ và gọi lại thông tin qua thời gian**, vượt ra ngoài một lượt hội thoại.

Bản thân LLM **không có trí nhớ** giữa các lần gọi. Mỗi lần nó chỉ thấy những gì được đưa vào context. Mọi "trí nhớ" đều do hệ thống bên ngoài lưu lại rồi **đưa ngược vào context** khi cần.

> **Hình dung:** LLM giống người bị mất trí nhớ ngắn hạn, mỗi sáng thức dậy không nhớ gì. Memory là cuốn sổ ghi chú đặt cạnh giường, được đọc lại mỗi sáng trước khi bắt đầu làm việc.

## 2. Vấn đề Memory giải quyết

- **Mỗi cuộc hội thoại bắt đầu từ số không:** người dùng phải giải thích lại bối cảnh, sở thích.
- **Context window có hạn:** hội thoại dài sẽ vượt giới hạn, thông tin cũ bị đẩy ra.
- **Không cá nhân hóa được:** agent không học được người dùng thích gì.
- **Tác vụ dài hơi:** việc kéo dài nhiều ngày cần nhớ đã làm gì, còn dang dở gì.

## 3. Các loại memory

**Theo thời gian:**

| Loại | Mô tả | Ví dụ |
|---|---|---|
| **Ngắn hạn** | Nội dung đang nằm trong context window của cuộc hội thoại hiện tại | Những gì bạn vừa nói 5 tin nhắn trước |
| **Dài hạn** | Lưu ngoài context, gọi lại khi cần | Sở thích của bạn từ tuần trước |

**Theo nội dung (mượn từ tâm lý học):**

| Loại | Lưu gì | Ví dụ |
|---|---|---|
| **Semantic** | Sự thật, sở thích | "Người dùng thích câu trả lời ngắn gọn" |
| **Episodic** | Sự kiện đã xảy ra | "Hôm qua đã debug lỗi đăng nhập thế nào" |
| **Procedural** | Cách làm việc | Quy tắc, quy trình đã học được |

## 4. Cách triển khai phổ biến

| Cách | Mô tả | Phù hợp khi |
|---|---|---|
| **Tóm tắt** | Nén hội thoại cũ thành bản tóm tắt ngắn rồi giữ trong context | Hội thoại dài nhưng chỉ cần nhớ ý chính |
| **Lưu file hoặc database** | Ghi các ghi chú quan trọng, đọc lại khi bắt đầu phiên mới | Cần nhớ rõ ràng, dễ kiểm soát và chỉnh sửa |
| **Vector database + retrieval** | Lưu ký ức dạng embedding, tìm ký ức liên quan theo ý nghĩa | Lượng ký ức lớn, không thể nhét hết vào context |

Cách thứ ba chính là **RAG áp dụng lên ký ức của agent** (xem [RAG](rag-overview.md)).

## 5. Vòng đời của một ký ức

1. **Ghi:** quyết định thông tin nào đáng lưu (từ hội thoại, kết quả tác vụ).
2. **Lưu:** đưa vào nơi lưu trữ, thường kèm thời gian và nhãn.
3. **Truy xuất:** khi có tình huống mới, tìm ký ức liên quan.
4. **Đưa vào context:** ghép ký ức tìm được vào prompt để LLM sử dụng.
5. **Cập nhật hoặc quên:** sửa khi thông tin đổi, xóa khi lỗi thời hoặc người dùng yêu cầu.

## 6. Vấn đề cần cân nhắc

- **Nhớ gì, quên gì:** không phải thứ gì cũng đáng lưu, lưu quá nhiều sẽ gây nhiễu.
- **Cập nhật và mâu thuẫn:** thông tin cũ sai đi thì xử lý thế nào (ví dụ người dùng đổi công ty).
- **Truy xuất sai:** nhớ nhầm hoặc kéo ký ức không liên quan vào làm lệch câu trả lời.
- **Quyền riêng tư:** người dùng cần biết, xem, sửa và xóa được những gì agent đã nhớ.

## 7. Tự kiểm tra

1. LLM không nhớ gì giữa các lần gọi, vậy agent "nhớ" bằng cách nào?
2. Memory ngắn hạn và dài hạn khác nhau ở đâu?
3. Semantic, episodic, procedural khác nhau thế nào? Cho ví dụ.
4. Vì sao lưu quá nhiều ký ức có thể làm agent kém đi?

## 8. Chủ đề để đào sâu sau

- Chiến lược tóm tắt và dọn dẹp ký ức
- Giải quyết mâu thuẫn giữa ký ức cũ và mới
- Memory dùng chung giữa nhiều agent
- Quyền riêng tư và kiểm soát của người dùng
- Memory vs RAG: ranh giới và chỗ chồng lấn (xem [file so sánh](so-sanh-moi-quan-he.md))
