# RAG, Skill, MCP, Memory: So sánh và mối quan hệ

> Chi tiết từng phần: [RAG](rag-overview.md) · [Skill](skill-overview.md) · [MCP](mcp-overview.md) · [Memory](memory-overview.md)

## 1. Bức tranh chung

Một AI agent cần giải quyết bốn câu hỏi khác nhau. Mỗi khái niệm trả lời một câu:

| Thành phần | Câu hỏi nó trả lời | Hình dung |
|---|---|---|
| **RAG** | Agent **biết gì**? | Tủ tài liệu để tra cứu |
| **Skill** | Agent **biết làm thế nào**? | Sổ tay quy trình |
| **MCP** | Agent **kết nối và làm được gì**? | Ổ cắm chuẩn để cắm công cụ |
| **Memory** | Agent **nhớ gì** qua thời gian? | Trí nhớ của nhân viên |

Tất cả đều xoay quanh một điểm chung: **LLM chỉ thấy những gì nằm trong context window**. Bốn thành phần này là bốn cách khác nhau để đưa đúng thứ cần thiết vào context, hoặc để mở rộng những gì agent làm được bên ngoài context.

## 2. Bảng so sánh

| | RAG | Skill | MCP | Memory |
|---|---|---|---|---|
| **Cung cấp** | Tri thức | Quy trình, cách làm | Kết nối và hành động | Ngữ cảnh theo thời gian |
| **Dạng nội dung** | Văn bản, tài liệu | Hướng dẫn, template, script | Tool, resource, prompt | Ghi chú, sự kiện, sở thích |
| **Nội dung do ai tạo** | Bạn nạp tài liệu | Bạn hoặc cộng đồng viết | Nhà cung cấp dịch vụ viết server | Agent tự ghi trong lúc dùng |
| **Thay đổi khi nào** | Khi tài liệu đổi | Khi quy trình đổi | Khi công cụ đổi | Liên tục theo từng tương tác |
| **Đưa vào context thế nào** | Tìm theo ý nghĩa, lấy vài đoạn | Nạp khi mô tả khớp | Kết quả tool trả về | Truy xuất ký ức liên quan |
| **Rủi ro chính** | Tìm sai đoạn | Mô tả kích hoạt sai | Quyền hạn, prompt injection | Nhớ sai, quyền riêng tư |

## 3. Các mối quan hệ cần nhớ

### Skill vs MCP

- **Skill** dạy agent *cách làm*, **MCP** cho agent *quyền và đường kết nối*.
- Ví dụ: MCP giúp agent đọc được lịch; skill dạy agent cách sắp xếp lịch theo quy tắc của bạn.
- MCP không có skill thì agent có công cụ nhưng không biết dùng đúng quy trình. Skill không có MCP thì agent biết quy trình nhưng không với tới dữ liệu thật.

### Memory vs RAG

- Memory thường được xây **bằng** RAG: lưu ký ức thành vector rồi tìm lại.
- Khác ở **nội dung và nguồn gốc**: RAG tra tài liệu có sẵn do con người nạp; memory lưu những gì phát sinh từ tương tác của chính agent.
- Khác ở **vòng đời**: kho RAG thường ổn định, ký ức thì liên tục được ghi, sửa, xóa.

### RAG vs MCP

- Cả hai đều đưa thông tin bên ngoài vào context, nhưng RAG tìm theo **ý nghĩa** trong kho đã được lập chỉ mục sẵn, còn MCP gọi **dịch vụ trực tiếp** và lấy dữ liệu hiện tại.
- Chúng chồng lấn: một kho RAG có thể được phơi ra qua một MCP server, khi đó retrieval là một tool (chính là agentic RAG).

### Skill vs RAG

- RAG lấy **thông tin** (sự thật, tài liệu). Skill lấy **hướng dẫn** (làm thế nào).
- Cả hai đều dùng ý tưởng "đừng nhét hết vào context, chỉ lấy khi cần", nhưng skill chọn theo mô tả, RAG chọn theo độ giống nhau về ý nghĩa.

### Skill vs Memory

- Skill là quy trình được **con người viết sẵn**. Memory procedural là cách làm mà agent **tự học được** qua thời gian.

## 4. Chọn cái nào khi nào?

| Nhu cầu của bạn | Dùng |
|---|---|
| Agent trả lời đúng theo tài liệu nội bộ, có dẫn nguồn | **RAG** |
| Agent làm một loại việc theo đúng chuẩn mỗi lần | **Skill** |
| Agent đọc email, lịch, database hoặc thực hiện hành động ở dịch vụ khác | **MCP** |
| Agent nhớ sở thích và bối cảnh qua nhiều phiên | **Memory** |
| Dữ liệu thay đổi từng phút (trạng thái đơn hàng, tồn kho) | **MCP** (gọi trực tiếp) hơn là RAG |
| Kho tài liệu lớn, cần tìm theo ý nghĩa | **RAG** |

## 5. Ví dụ tổng hợp

**Tình huống:** bạn nhờ agent *"Soạn thư trả lời khách hàng A về việc hoàn tiền, theo chính sách công ty và giọng văn thường dùng của tôi."*

| Bước | Thành phần | Việc xảy ra |
|---|---|---|
| 1 | **MCP** | Agent đọc email của khách hàng A qua MCP server của hộp thư |
| 2 | **RAG** | Agent tra kho tài liệu để tìm đúng điều khoản hoàn tiền |
| 3 | **Memory** | Agent nhớ bạn thích giọng văn lịch sự, ngắn gọn và từng xử lý khách A trước đó |
| 4 | **Skill** | Agent nạp skill "thư phản hồi khách hàng" để theo đúng cấu trúc và template công ty |
| 5 | **MCP** | Agent lưu bản nháp vào hộp thư qua MCP |

Cả bốn thành phần cùng tham gia, mỗi thành phần đảm nhận một phần việc khác nhau.

## 6. Những khái niệm liên quan nên biết

| Khái niệm | Vai trò trong bức tranh này |
|---|---|
| **Context window** | Giới hạn mà cả bốn thành phần đều xoay quanh |
| **Embedding và vector database** | Nền tảng kỹ thuật của RAG và một cách làm memory |
| **Tool use / function calling** | Cơ chế để LLM gọi công cụ; MCP chuẩn hóa phần kết nối |
| **Agent loop** | Vòng lặp suy nghĩ → hành động → quan sát mà các thành phần trên phục vụ |
| **Prompt injection** | Rủi ro chung khi dữ liệu từ RAG, MCP hoặc memory chứa chỉ dẫn độc hại |
| **Fine-tuning** | Hướng thay thế: đưa tri thức hoặc hành vi vào chính mô hình thay vì cung cấp qua context |

## 7. Tự kiểm tra

1. Mỗi thành phần trong bốn cái trả lời câu hỏi nào về agent?
2. Vì sao nói Skill và MCP bổ sung cho nhau?
3. Memory và RAG khác nhau ở đâu, dù cùng có thể dùng vector database?
4. Dữ liệu tồn kho thay đổi từng phút nên dùng RAG hay MCP? Vì sao?
5. Trong ví dụ hoàn tiền ở trên, nếu thiếu mỗi thành phần thì agent hỏng ở đâu?

## 8. Gợi ý thứ tự học

1. **LLM và context window:** hiểu giới hạn gốc.
2. **Embedding và vector database:** nền cho RAG và memory.
3. **RAG:** thành phần tri thức.
4. **Tool use và MCP:** thành phần hành động.
5. **Memory:** xây trên nền RAG và tool.
6. **Skill:** gói quy trình, dễ hiểu hơn khi bạn đã thấy agent làm việc thật.
7. **Agent loop và agentic RAG:** ghép tất cả lại.
