# Mối quan hệ của RAG, Skill, MCP, Memory với AI Agent

> Các file liên quan: [AI Agent](ai-agent.md) · [RAG](rag-overview.md) · [Skill](skill-overview.md) · [MCP](mcp-overview.md) · [Memory](memory-overview.md) · [So sánh](so-sanh-moi-quan-he.md)

## 1. Ý tưởng trung tâm

[AI agent](ai-agent.md) = **LLM + vòng lặp hành động**. Nhưng LLM tự nó thiếu bốn thứ để làm việc thật:

| LLM thiếu gì | Thành phần bù vào | Câu hỏi nó trả lời |
|---|---|---|
| Không biết dữ liệu riêng và mới | **RAG** | Agent **biết gì**? |
| Không biết quy trình cụ thể của bạn | **Skill** | Agent **biết làm thế nào**? |
| Không với tới thế giới bên ngoài | **MCP** | Agent **làm được gì**, kết nối tới đâu? |
| Không nhớ gì giữa các lần gọi | **Memory** | Agent **nhớ gì** qua thời gian? |

> **Hình dung:** LLM là một người rất thông minh nhưng vừa bước vào công ty ngày đầu, không có tài liệu, không có hướng dẫn, không có máy tính, và mỗi sáng quên hết. RAG là kho tài liệu, Skill là sổ tay quy trình, MCP là bộ tài khoản và phần mềm được cấp, Memory là cuốn sổ ghi chú cá nhân.

Điểm chung: **LLM chỉ "thấy" những gì nằm trong context window**. Bốn thành phần này là cách đưa đúng thứ cần thiết vào context đúng lúc, hoặc cho agent tác động ra ngoài context.

## 2. Bốn thành phần nằm ở đâu trong kiến trúc agent?

```
                 Người dùng / sự kiện
                         │ mục tiêu
                         ▼
   ┌────────────────────────────────────────────────────┐
   │                  VÒNG LẶP AGENT                    │
   │                                                    │
   │   ┌────────────────────────────────────────────┐   │
   │   │            CONTEXT WINDOW (LLM)            │   │
   │   │  hướng dẫn · mục tiêu · lịch sử · kết quả  │   │
   │   └───▲──────────▲──────────▲──────────▲───────┘   │
   │       │          │          │          │           │
   │    ┌──┴───┐  ┌───┴───┐  ┌───┴───┐  ┌───┴────┐      │
   │    │ RAG  │  │ Skill │  │Memory │  │  MCP   │      │
   │    │ tri  │  │ quy   │  │ ký ức │  │ công cụ│      │
   │    │ thức │  │ trình │  │       │  │ & dữ   │      │
   │    │      │  │       │  │       │  │ liệu   │      │
   │    └──────┘  └───────┘  └───────┘  └───┬────┘      │
   │                                        │           │
   └────────────────────────────────────────┼───────────┘
                                            ▼
                               Thế giới bên ngoài
                          (email, lịch, database, web...)
```

Ba thành phần RAG, Skill, Memory chủ yếu **đưa thông tin vào context**. MCP vừa đưa dữ liệu vào, vừa là **cửa ra** để agent hành động.

## 3. Vai trò của từng thành phần với agent

### 3.1 RAG: tri thức

**Agent dùng RAG để:**
- Tra cứu chính sách, tài liệu sản phẩm, wiki nội bộ trước khi trả lời.
- Có bằng chứng và dẫn nguồn, giảm hallucination.
- Làm việc với kho tài liệu lớn hơn nhiều so với context window.

**Trong vòng lặp:** thường là một **bước quan sát**: agent quyết định cần tra cứu, gọi retrieval, nhận các đoạn liên quan rồi tiếp tục suy luận.

**Thiếu RAG thì:** agent trả lời dựa vào kiến thức chung (có thể lỗi thời hoặc bịa), không biết gì về dữ liệu riêng của bạn.

### 3.2 Skill: quy trình

**Agent dùng Skill để:**
- Làm một loại việc theo đúng chuẩn, nhất quán mỗi lần.
- Nạp hướng dẫn chi tiết chỉ khi cần, không làm đầy context.
- Kèm template và script cho đầu ra đúng định dạng.

**Trong vòng lặp:** ở bước **lập kế hoạch**: nhận ra nhiệm vụ khớp một skill, nạp skill, rồi hành động theo hướng dẫn trong đó.

**Thiếu Skill thì:** agent vẫn làm được nhưng theo cách "tự nghĩ", chất lượng và định dạng không đều, bạn phải nhắc lại yêu cầu mỗi lần.

### 3.3 MCP: công cụ và kết nối

**Agent dùng MCP để:**
- Đọc dữ liệu hiện tại từ dịch vụ (hộp thư, lịch, database, tracker).
- Thực hiện hành động (gửi, tạo, cập nhật, xóa).
- Cắm thêm công cụ mới theo chuẩn chung mà không phải viết tích hợp riêng.

**Trong vòng lặp:** là bước **hành động và quan sát**: agent chọn tool, MCP chuyển yêu cầu tới dịch vụ, kết quả trả về context.

**Thiếu MCP (hoặc cơ chế công cụ tương đương) thì:** agent chỉ nói được, không làm được. Nó là chatbot, không phải agent.

### 3.4 Memory: ký ức

**Agent dùng Memory để:**
- Nhớ sở thích, bối cảnh người dùng qua nhiều phiên.
- Giữ tiến độ của tác vụ dài hơi.
- Rút kinh nghiệm từ lần làm trước.

**Trong vòng lặp:** ở **đầu** (nạp ký ức liên quan vào context) và **cuối** hoặc giữa chừng (ghi lại điều đáng nhớ).

**Thiếu Memory thì:** mỗi cuộc hội thoại bắt đầu từ số không; người dùng phải giải thích lại bối cảnh, agent không cá nhân hóa được.

## 4. Ánh xạ vào từng bước của vòng lặp agent

| Bước của vòng lặp | Thành phần tham gia | Việc xảy ra |
|---|---|---|
| **Nhận mục tiêu** | Memory | Nạp ký ức liên quan: sở thích, bối cảnh trước đó |
| **Lập kế hoạch** | Skill | Nhận ra loại việc, nạp quy trình tương ứng |
| **Cần thông tin** | RAG | Tra kho tri thức để lấy sự thật, chính sách |
| **Hành động** | MCP | Gọi tool để đọc dữ liệu hiện tại hoặc thực hiện thao tác |
| **Quan sát** | MCP, RAG | Kết quả trả về được đưa vào context |
| **Kết thúc** | Memory | Ghi lại kết quả, điều học được, việc còn dang dở |

## 5. Ví dụ chi tiết: agent hỗ trợ khách hàng

**Yêu cầu:** nhân viên nhờ *"Khách hàng A (đơn #1234) xin hoàn tiền, soạn thư trả lời giúp tôi."*

| # | Bước | Thành phần | Chi tiết |
|---|---|---|---|
| 1 | Nạp ký ức | **Memory** | Nhớ: nhân viên thích thư ngắn gọn, khách A từng khiếu nại tháng trước |
| 2 | Nhận ra loại việc | **Skill** | Khớp skill "thư phản hồi khách hàng": nạp cấu trúc và giọng văn chuẩn |
| 3 | Lấy dữ liệu đơn hàng | **MCP** | Gọi tool hệ thống đơn hàng: đơn #1234, giao ngày 3/10, đã dùng |
| 4 | Tra chính sách | **RAG** | Tìm đoạn chính sách hoàn tiền: hoàn trong 7 ngày nếu chưa dùng |
| 5 | Suy luận | LLM | Đơn đã dùng nên không đủ điều kiện hoàn tiền toàn phần, nhưng có thể đề nghị đổi hàng |
| 6 | Soạn thư | **Skill** + LLM | Viết theo template, giọng lịch sự, ngắn gọn, nêu rõ lý do và phương án thay thế |
| 7 | Lưu bản nháp | **MCP** | Gọi tool lưu nháp vào hộp thư, chờ nhân viên duyệt |
| 8 | Ghi nhớ | **Memory** | Ghi: khách A khiếu nại hoàn tiền lần hai, đã đề nghị đổi hàng |

**Thử bỏ từng thành phần để thấy vai trò của nó:**

| Nếu thiếu | Hậu quả |
|---|---|
| Memory | Không biết khách A từng khiếu nại, không biết sở thích văn phong của nhân viên |
| Skill | Thư đúng nội dung nhưng sai cấu trúc, sai giọng văn chuẩn công ty |
| MCP | Không lấy được dữ liệu đơn hàng, không lưu được bản nháp; nhân viên phải dán thông tin thủ công |
| RAG | Phải đoán chính sách hoàn tiền, nguy cơ hứa sai với khách |

## 6. Các mẫu kết hợp thường gặp

| Mẫu | Mô tả |
|---|---|
| **Agentic RAG** | Retrieval là một tool: agent tự quyết định có tra không, tra gì, tra lại nếu chưa đủ |
| **RAG qua MCP** | Kho tri thức được phơi ra bằng một MCP server, nhiều agent dùng chung |
| **Memory dựa trên RAG** | Ký ức lưu thành vector rồi tìm lại theo ý nghĩa |
| **Memory như một tool** | Agent chủ động gọi "ghi nhớ" và "nhớ lại" như gọi công cụ |
| **Skill + MCP** | MCP cho quyền truy cập, Skill dạy cách dùng quyền đó đúng quy trình |
| **Skill chứa script gọi tool** | Skill đóng gói cả hướng dẫn lẫn các bước gọi công cụ cố định |

## 7. Chọn thành phần theo mức trưởng thành của agent

| Mức | Agent có | Khả năng |
|---|---|---|
| **1** | Chỉ LLM + system prompt | Trả lời câu hỏi chung |
| **2** | + RAG | Trả lời đúng theo tài liệu riêng |
| **3** | + MCP (công cụ) | Làm được việc thật: đọc, ghi, hành động |
| **4** | + Skill | Làm việc theo quy trình chuẩn, nhất quán |
| **5** | + Memory | Cá nhân hóa, làm việc dài hạn, rút kinh nghiệm |

Không bắt buộc đi đủ năm mức. Hãy thêm thành phần khi **có nhu cầu rõ ràng**, vì mỗi thành phần thêm vào đều tăng độ phức tạp, chi phí và bề mặt rủi ro.

## 8. Cân bằng context: bài toán chung

Cả bốn thành phần đều muốn đưa thông tin vào context, nhưng context có hạn và nhiễu làm agent kém đi.

| Thành phần | Cách tiết kiệm context |
|---|---|
| **RAG** | Chỉ lấy vài đoạn liên quan nhất, rerank trước khi đưa vào |
| **Skill** | Mô tả ngắn luôn có sẵn, nội dung đầy đủ chỉ nạp khi khớp |
| **MCP** | Chỉ bật các server cần thiết; kết quả tool nên gọn |
| **Memory** | Truy xuất có chọn lọc, tóm tắt ký ức cũ, xóa cái lỗi thời |

**Nguyên tắc:** đưa vào context *đúng thứ cần, đúng lúc*, không phải *mọi thứ có thể cần*.

## 9. Lỗi thường gặp theo từng thành phần

| Thành phần | Lỗi điển hình |
|---|---|
| **RAG** | Tìm sai đoạn; đoạn bị cắt mất ngữ cảnh; tài liệu mâu thuẫn |
| **Skill** | Mô tả mơ hồ nên kích hoạt sai hoặc không kích hoạt; hướng dẫn thiếu rõ |
| **MCP** | Gọi sai tool; tool mô tả kém; quyền quá rộng; kết quả tool quá dài |
| **Memory** | Nhớ sai; ký ức cũ mâu thuẫn ký ức mới; kéo ký ức không liên quan |

## 10. Bảo mật: rủi ro chung

Cả RAG, MCP và Memory đều đưa **nội dung từ bên ngoài** vào context, và nội dung đó có thể chứa chỉ dẫn độc hại (prompt injection). Skill cũng có thể chứa script nên cần tin cậy nguồn.

**Biện pháp chung:**
- Coi nội dung lấy về là **dữ liệu**, không phải mệnh lệnh.
- Cấp quyền tối thiểu cho từng MCP server.
- Yêu cầu xác nhận của người dùng với hành động có hậu quả.
- Chỉ cài skill và server từ nguồn tin cậy.
- Cho người dùng xem, sửa, xóa được ký ức của agent.

## 11. Tổng kết: một câu cho mỗi thành phần

- **Agent** là người làm việc: có mục tiêu, tự quyết định, hành động.
- **RAG** cho agent *tri thức* để không bịa.
- **Skill** cho agent *quy trình* để làm đúng và nhất quán.
- **MCP** cho agent *tay chân* để chạm vào thế giới thật.
- **Memory** cho agent *ký ức* để không bắt đầu lại từ đầu.

## 12. Tự kiểm tra

1. LLM thiếu bốn thứ gì, và mỗi thành phần bù vào chỗ nào?
2. Ở bước nào của vòng lặp agent thì Memory, Skill, RAG, MCP tham gia?
3. Trong ví dụ hỗ trợ khách hàng, nếu bỏ RAG thì rủi ro cụ thể là gì?
4. Vì sao nói "thêm thành phần khi có nhu cầu rõ ràng" thay vì dùng đủ cả bốn?
5. Điểm chung của bốn thành phần liên quan đến context window là gì?
6. Rủi ro bảo mật nào chung cho RAG, MCP và Memory?

## 13. Gợi ý thứ tự học tiếp

1. **Tool use / function calling:** cơ chế gốc để agent hành động (nền của MCP).
2. **Agent loop và các mẫu lập kế hoạch:** ReAct, plan-and-execute.
3. **Agentic RAG:** chỗ RAG và agent gặp nhau.
4. **Quản lý context:** bài toán chung của cả bốn thành phần.
5. **Đánh giá và quan sát agent:** biết agent sai ở đâu.
6. **Bảo mật agent:** prompt injection và giới hạn quyền.
