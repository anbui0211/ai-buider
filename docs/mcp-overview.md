# MCP (Model Context Protocol): Tổng quan

> Các file liên quan: [RAG](rag-overview.md) · [Skill](skill-overview.md) · [Memory](memory-overview.md) · [So sánh và mối quan hệ](so-sanh-moi-quan-he.md)

## 1. MCP là gì?

**MCP** là một **giao thức mở** chuẩn hóa cách ứng dụng AI kết nối với **công cụ và nguồn dữ liệu bên ngoài** (email, lịch, Drive, database, GitHub, Slack...).

> **Hình dung:** giống cổng **USB-C**. Trước đây mỗi thiết bị cần một loại dây riêng; có chuẩn chung thì cắm được mọi thứ.

## 2. Vấn đề MCP giải quyết

LLM tự nó chỉ nhận chữ và trả chữ, không đọc được email hay tạo được sự kiện lịch. Muốn làm được, phải nối nó với dịch vụ bên ngoài.

Nếu có *N* ứng dụng AI và *M* công cụ, không có chuẩn chung nghĩa là phải viết tới **N × M** tích hợp riêng. Với MCP, mỗi bên chỉ cần tuân theo chuẩn một lần: công cụ viết **một MCP server**, mọi ứng dụng hỗ trợ MCP đều dùng được.

## 3. Kiến trúc cơ bản

```
[Host: ứng dụng AI]  ──  [MCP Client]  ⇄  [MCP Server]  ──  [Dịch vụ/Dữ liệu thật]
  (ví dụ: app chat,        (nằm trong host)   (bọc quanh        (Gmail, DB,
   trình soạn code)                            một dịch vụ)       file hệ thống...)
```

| Thành phần | Vai trò |
|---|---|
| **Host** | Ứng dụng AI mà người dùng tương tác |
| **Client** | Phần trong host, giữ kết nối tới một server |
| **Server** | Chương trình "bọc" một dịch vụ và phơi ra các khả năng theo chuẩn MCP |

## 4. Một MCP server cung cấp gì?

| Loại | Ý nghĩa | Ví dụ |
|---|---|---|
| **Tools** | Hành động agent có thể gọi | Gửi email, tạo sự kiện lịch |
| **Resources** | Dữ liệu agent có thể đọc | Nội dung file, bản ghi database |
| **Prompts** | Mẫu prompt dựng sẵn | Mẫu "tóm tắt cuộc họp" |

## 5. Luồng hoạt động khi dùng một tool

1. Host kết nối tới server và **lấy danh sách tool** (tên, mô tả, tham số).
2. Danh sách này được đưa cho LLM như các công cụ có thể dùng.
3. Người dùng yêu cầu việc cần tool, LLM **quyết định gọi tool nào** với tham số gì.
4. Host chuyển yêu cầu tới server, server thực thi và **trả kết quả**.
5. Kết quả được đưa lại cho LLM để nó tiếp tục và trả lời người dùng.

## 6. Ví dụ

Bạn kết nối MCP server của lịch. Khi hỏi "Ngày mai tôi rảnh lúc nào?", agent gọi tool đọc lịch, nhận danh sách sự kiện, rồi trả lời dựa trên đó. Nếu bạn nói "đặt họp lúc 3 giờ chiều", agent gọi tool tạo sự kiện.

## 7. Lưu ý khi dùng

- **Quyền hạn:** chỉ cấp quyền tối thiểu cần thiết cho mỗi server.
- **Độ tin cậy:** chỉ kết nối server từ nguồn đáng tin.
- **Prompt injection:** dữ liệu lấy về từ công cụ (một email, một trang web) có thể chứa chỉ dẫn độc hại. Agent cần coi đó là dữ liệu chứ không phải mệnh lệnh.
- **Hành động có hậu quả:** với thao tác như gửi, xóa, thanh toán, nên có bước người dùng xác nhận.

## 8. Tự kiểm tra

1. MCP giải quyết vấn đề gì, và vì sao ví với USB-C?
2. Tool, resource, prompt khác nhau thế nào?
3. Trong luồng gọi tool, ai quyết định gọi tool nào, ai thực thi?
4. Vì sao dữ liệu lấy về qua MCP có thể là rủi ro bảo mật?

## 9. Chủ đề để đào sâu sau

- Viết MCP server đầu tiên
- Các kiểu transport (chạy local và từ xa)
- Xác thực và phân quyền
- Bảo mật trước prompt injection
- MCP vs Skill: khi nào dùng cái nào (xem [file so sánh](so-sanh-moi-quan-he.md))
