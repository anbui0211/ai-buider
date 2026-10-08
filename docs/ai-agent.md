# AI Agent: Tìm hiểu chi tiết

> Các file liên quan: [Agent và 4 thành phần](ai-agent-va-4-thanh-phan.md) · [RAG](rag-overview.md) · [Skill](skill-overview.md) · [MCP](mcp-overview.md) · [Memory](memory-overview.md) · [So sánh](so-sanh-moi-quan-he.md)

## 1. AI agent là gì?

**AI agent** là hệ thống dùng LLM làm "bộ não" để **tự đạt một mục tiêu**: nó lập kế hoạch, chọn hành động, dùng công cụ, quan sát kết quả và điều chỉnh, lặp lại cho đến khi xong.

Điểm cốt lõi là **tính tự chủ trong quyết định**: bạn đưa ra *mục tiêu*, agent tự tìm *đường đi*.

> **Hình dung:** LLM thuần giống một chuyên gia chỉ trả lời câu hỏi qua điện thoại. Agent giống một nhân viên được giao việc: họ tự tra cứu, gọi điện, mở phần mềm, làm từng bước và báo lại khi xong.

## 2. Agent khác gì LLM và chatbot?

| | LLM thuần | Chatbot | AI agent |
|---|---|---|---|
| **Làm gì** | Nhận chữ, trả chữ | Hội thoại theo lượt | Theo đuổi mục tiêu qua nhiều bước |
| **Dùng công cụ** | Không | Hạn chế hoặc theo kịch bản | Có, tự quyết định gọi cái nào |
| **Tác động thế giới thật** | Không | Thường không | Có (gửi email, sửa file, đặt lịch...) |
| **Ai quyết định bước tiếp theo** | Người dùng | Người dùng hoặc kịch bản | Chính agent |
| **Số bước cho một yêu cầu** | Một | Một lượt | Nhiều bước, lặp |

Ranh giới không rạch ròi: một chatbot có thêm khả năng gọi công cụ và tự lặp thì đã tiến dần thành agent.

## 3. Các thành phần của một agent

```
                ┌──────────────────────────────┐
                │        MỤC TIÊU / NHIỆM VỤ   │
                └──────────────┬───────────────┘
                               ▼
   ┌──────────────────────────────────────────────────────┐
   │                     LLM (bộ não)                     │
   │          suy luận · lập kế hoạch · ra quyết định     │
   └───────┬──────────────┬──────────────┬────────────────┘
           │              │              │
           ▼              ▼              ▼
     ┌──────────┐   ┌──────────┐   ┌─────────────┐
     │ Công cụ  │   │ Tri thức │   │   Bộ nhớ    │
     │ (tools)  │   │ & quy    │   │             │
     │          │   │ trình    │   │             │
     └──────────┘   └──────────┘   └─────────────┘
```

| Thành phần | Vai trò |
|---|---|
| **LLM** | Hiểu mục tiêu, suy luận, quyết định bước tiếp theo |
| **Hướng dẫn (system prompt)** | Vai trò, quy tắc, giới hạn, phong cách của agent |
| **Công cụ (tools)** | Cách agent tác động và thu thập thông tin bên ngoài |
| **Tri thức** | Thông tin agent tra cứu để trả lời đúng |
| **Quy trình** | Cách làm chuẩn cho từng loại việc |
| **Bộ nhớ** | Những gì agent giữ lại qua thời gian |
| **Vòng lặp điều khiển** | Mã bao quanh LLM để chạy chu trình hành động |

Bốn thành phần RAG, Skill, MCP, Memory chính là các hiện thực hóa cụ thể của tri thức, quy trình, công cụ và bộ nhớ. Chi tiết xem [file quan hệ](ai-agent-va-4-thanh-phan.md).

## 4. Vòng lặp agent (agent loop)

Đây là cơ chế trung tâm. Agent lặp chu trình:

1. **Nhận mục tiêu** (từ người dùng hoặc một sự kiện).
2. **Suy nghĩ / lập kế hoạch:** cần làm gì để tiến gần mục tiêu?
3. **Hành động:** gọi một công cụ hoặc tạo câu trả lời.
4. **Quan sát:** đọc kết quả công cụ trả về.
5. **Đánh giá:** xong chưa? Có lỗi không? Cần đổi hướng không?
6. **Lặp lại bước 2** hoặc **kết thúc** và báo cáo.

Mô hình này nổi tiếng với tên **ReAct** (Reason + Act): xen kẽ suy luận và hành động.

### Ví dụ chạy thực tế

**Mục tiêu:** *"Tìm quán cà phê yên tĩnh gần văn phòng và đặt bàn cho 4 người lúc 3 giờ chiều mai."*

| Vòng | Suy nghĩ | Hành động | Quan sát |
|---|---|---|---|
| 1 | Cần danh sách quán gần văn phòng | Gọi tool tìm địa điểm | Có 8 quán |
| 2 | Cần lọc quán yên tĩnh, còn chỗ | Gọi tool xem đánh giá và giờ mở cửa | Còn 3 quán phù hợp |
| 3 | Chọn quán tốt nhất, thử đặt bàn | Gọi tool đặt bàn | Hết chỗ lúc 3 giờ |
| 4 | Thử quán thứ hai | Gọi tool đặt bàn | Đặt thành công |
| 5 | Xong, báo lại cho người dùng | Trả lời | Kết thúc |

Chú ý vòng 3: bước thất bại khiến agent **tự đổi hướng**. Đây là điều một kịch bản cố định không làm được.

## 5. Workflow và agent: hai thái cực

Không phải mọi hệ thống dùng LLM đều nên là agent.

| | Workflow | Agent |
|---|---|---|
| **Đường đi** | Con người định sẵn các bước | LLM tự quyết định các bước |
| **Tính dự đoán** | Cao | Thấp hơn |
| **Linh hoạt** | Chỉ xử lý được kịch bản đã lường trước | Xử lý được tình huống mới |
| **Chi phí và độ trễ** | Thấp hơn | Cao hơn (nhiều lần gọi LLM) |
| **Phù hợp** | Quy trình ổn định, lặp lại | Bài toán mở, khó biết trước các bước |

**Nguyên tắc thực tế:** bắt đầu từ giải pháp đơn giản nhất (một lần gọi LLM, rồi workflow), chỉ lên agent khi sự linh hoạt thật sự cần thiết.

## 6. Các mức tự chủ

| Mức | Mô tả | Ví dụ |
|---|---|---|
| **Trợ lý** | Gợi ý, người dùng tự làm | Gợi ý nội dung email |
| **Bán tự chủ** | Agent làm, người dùng duyệt các bước quan trọng | Soạn email và hỏi trước khi gửi |
| **Tự chủ** | Agent làm trọn vẹn, người dùng xem kết quả | Tự xử lý hộp thư theo quy tắc |

Mức tự chủ nên **tăng dần theo độ tin cậy** đã chứng minh, và tỉ lệ nghịch với hậu quả của sai sót.

## 7. Single-agent và multi-agent

- **Single-agent:** một agent với đủ công cụ. Đơn giản, dễ gỡ lỗi. Nên là lựa chọn mặc định.
- **Multi-agent:** nhiều agent chuyên biệt phối hợp (ví dụ một agent nghiên cứu, một agent viết, một agent kiểm tra). Hữu ích khi nhiệm vụ quá lớn, cần chia context, hoặc cần góc nhìn độc lập. Đổi lại là phức tạp, tốn chi phí, khó theo dõi.

Các mẫu phối hợp thường gặp: **điều phối viên giao việc cho agent con**, **chuỗi nối tiếp** (đầu ra agent này là đầu vào agent kia), **chạy song song rồi tổng hợp**, **một agent soạn một agent phản biện**.

## 8. Ứng dụng điển hình

- **Hỗ trợ khách hàng:** tra chính sách, xem đơn hàng, xử lý hoàn tiền.
- **Lập trình:** đọc codebase, sửa lỗi, chạy test, đề xuất thay đổi.
- **Nghiên cứu:** tìm nguồn, đọc, đối chiếu, tổng hợp báo cáo.
- **Trợ lý cá nhân:** quản lý email, lịch, ghi chú.
- **Vận hành dữ liệu:** truy vấn, làm sạch, tạo báo cáo.

## 9. Thách thức và rủi ro

| Thách thức | Giải thích |
|---|---|
| **Lỗi tích lũy** | Mỗi bước có xác suất sai nhỏ, qua nhiều bước thì xác suất thành công cả chuỗi giảm mạnh |
| **Lạc hướng / lặp vô hạn** | Agent mắc kẹt hoặc đi lòng vòng mà không tiến gần mục tiêu |
| **Chi phí và độ trễ** | Nhiều lần gọi LLM và công cụ cho một yêu cầu |
| **Hành động không thể hoàn tác** | Gửi nhầm, xóa nhầm, thanh toán nhầm |
| **Prompt injection** | Nội dung độc hại trong dữ liệu (email, trang web) lừa agent làm theo |
| **Khó đánh giá** | Nhiều đường đi khả dĩ, khó kiểm tra đúng sai bằng một bài test cố định |
| **Khó gỡ lỗi** | Lỗi có thể nằm ở bất kỳ bước nào trong chuỗi |

## 10. Nguyên tắc thiết kế

1. **Đơn giản trước:** không dùng agent khi workflow đủ dùng.
2. **Công cụ rõ ràng:** tên, mô tả và tham số tool dễ hiểu; chất lượng tool ảnh hưởng trực tiếp chất lượng agent.
3. **Giới hạn quyền:** cấp quyền tối thiểu; tách thao tác đọc và thao tác ghi.
4. **Người trong vòng lặp:** yêu cầu xác nhận với hành động rủi ro cao.
5. **Có điều kiện dừng:** giới hạn số bước, thời gian, chi phí.
6. **Minh bạch:** ghi lại từng bước để theo dõi và gỡ lỗi.
7. **Đánh giá liên tục:** có bộ tình huống kiểm tra, đo cả kết quả cuối lẫn đường đi.

## 11. Tự kiểm tra

1. Agent khác chatbot ở điểm cốt lõi nào?
2. Mô tả vòng lặp agent bằng lời của bạn.
3. Khi nào nên dùng workflow thay vì agent?
4. Vì sao lỗi tích lũy là vấn đề lớn với agent nhiều bước?
5. Vì sao nên bắt đầu bằng single-agent?

## 12. Chủ đề để đào sâu sau

- Tool use / function calling
- Các mẫu lập kế hoạch (ReAct, plan-and-execute, reflection)
- Multi-agent và các mẫu phối hợp
- Đánh giá và quan sát (evaluation, tracing)
- Bảo mật agent (prompt injection, giới hạn quyền)
- Quản lý context trong tác vụ dài
