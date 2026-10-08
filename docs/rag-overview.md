# RAG (Retrieval-Augmented Generation): Tổng quan

## 1. RAG là gì?

**RAG** là kỹ thuật cho phép mô hình ngôn ngữ (LLM) **tra cứu thông tin bên ngoài trước khi trả lời**, thay vì chỉ dựa vào những gì nó đã học lúc huấn luyện.

> **Hình dung:** giống như thi *open-book*. Thay vì bắt học sinh thuộc lòng mọi thứ, cho phép họ mở tài liệu, tìm đúng đoạn cần thiết rồi mới viết câu trả lời.

## 2. Vấn đề RAG giải quyết

LLM có ba hạn chế lớn:

| Hạn chế | Mô tả |
|---|---|
| **Kiến thức bị đóng băng** | Chỉ biết đến thời điểm huấn luyện, không biết dữ liệu mới. |
| **Không biết dữ liệu riêng** | Tài liệu nội bộ công ty, hợp đồng, wiki... không nằm trong mô hình. |
| **Hallucination** | Khi không biết, vẫn có xu hướng trả lời nghe rất tự tin nhưng sai. |

Có thể fine-tune lại mô hình, nhưng tốn kém và khó cập nhật liên tục. RAG rẻ và linh hoạt hơn: chỉ cần cập nhật kho tài liệu.

## 3. RAG hoạt động thế nào?

### Giai đoạn chuẩn bị (làm một lần, cập nhật khi có dữ liệu mới)

1. **Chunking:** chia tài liệu thành các đoạn nhỏ.
2. **Embedding:** chuyển mỗi đoạn thành vector số mô tả ý nghĩa của nó.
3. **Lưu trữ:** lưu các vector vào cơ sở dữ liệu vector (vector database).

### Khi có câu hỏi

1. Câu hỏi được chuyển thành embedding.
2. **Retrieval:** hệ thống tìm các đoạn có ý nghĩa gần nhất với câu hỏi.
3. **Augmentation:** các đoạn tìm được được ghép vào prompt cùng câu hỏi.
4. **Generation:** LLM đọc và trả lời dựa trên tài liệu đó, thường kèm trích dẫn nguồn.

```
Tài liệu → Chunk → Embedding → Vector DB
                                   ↑
Câu hỏi → Embedding → Tìm đoạn gần nhất
                                   ↓
              Prompt (câu hỏi + đoạn tìm được) → LLM → Câu trả lời
```

## 4. RAG phục vụ gì cho AI agent?

AI agent là hệ thống dùng LLM để tự lập kế hoạch, ra quyết định và thực hiện hành động. RAG đóng vai trò **bộ nhớ dài hạn và nguồn tri thức** của agent:

- **Cung cấp kiến thức chuyên ngành:** ví dụ agent hỗ trợ khách hàng tra cứu chính sách, FAQ, tài liệu sản phẩm.
- **Giảm hallucination:** trả lời dựa trên bằng chứng cụ thể, có thể dẫn nguồn để kiểm chứng.
- **Bộ nhớ dài hạn:** lưu và truy xuất lại hội thoại cũ, sở thích người dùng, kết quả tác vụ trước, vượt ra ngoài giới hạn context window.
- **Dữ liệu luôn cập nhật:** chỉ cần cập nhật kho tài liệu, không cần huấn luyện lại.
- **Kiểm soát quyền truy cập:** giới hạn agent chỉ truy xuất tài liệu mà người dùng được phép xem.

### Agentic RAG

- **RAG cơ bản:** chạy theo đường thẳng: hỏi → tìm → trả lời.
- **Agentic RAG:** agent tự quyết định *có cần tìm không*, tìm bằng truy vấn nào, ở nguồn nào, và tìm lại hoặc kết hợp nhiều nguồn nếu kết quả chưa đủ. Retrieval trở thành một **tool** mà agent chủ động gọi, giống như gọi API hay chạy code.

## 5. Hạn chế chính

Chất lượng RAG phụ thuộc rất nhiều vào khâu tìm kiếm. Nếu chunking kém hoặc retrieval lấy sai đoạn, LLM sẽ trả lời sai dù bản thân nó rất giỏi. Vì vậy phần lớn công sức tối ưu RAG nằm ở chia đoạn, chọn embedding và xếp hạng lại kết quả (*reranking*).

## 6. Tự kiểm tra

Sau khi đọc, bạn nên trả lời được bằng lời của mình:

1. RAG khác với việc hỏi LLM trực tiếp ở điểm nào?
2. Dữ liệu đi qua những bước nào từ tài liệu gốc đến câu trả lời?
3. Agent dùng RAG để làm gì?

## 7. Liên hệ với các mục khác

| Mục khác | Vị trí trong RAG |
|---|---|
| Embedding | Bước 2 của giai đoạn chuẩn bị, và bước 1 khi có câu hỏi |
| Vector database | Bước 3 của giai đoạn chuẩn bị, và nơi thực hiện tìm kiếm |
| Prompt / LLM | Bước augmentation và generation |
| AI agent / tool use | Agentic RAG: retrieval là một tool |

## 8. Chủ đề để đào sâu sau

- Pipeline chi tiết (indexing và query time)
- Chunking: kích thước, overlap, chia theo cấu trúc tài liệu
- Retrieval nâng cao: hybrid search (BM25 + vector), reranking, query rewriting, lọc metadata
- Thiết kế prompt cho RAG: bám tài liệu, dẫn nguồn, nói "không biết"
- Đánh giá RAG: tách retrieval và faithfulness
- Các lỗi thường gặp
- RAG vs fine-tuning vs long context
