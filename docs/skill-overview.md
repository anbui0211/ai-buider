# Skill: Tổng quan

> Các file liên quan: [RAG](rag-overview.md) · [MCP](mcp-overview.md) · [Memory](memory-overview.md) · [So sánh và mối quan hệ](so-sanh-moi-quan-he.md)

## 1. Skill là gì?

**Skill** là một gói hướng dẫn đóng sẵn, dạy agent cách thực hiện **một loại công việc cụ thể** theo đúng quy trình. Thường là một thư mục gồm:

- Một file hướng dẫn chính (ví dụ `SKILL.md`) mô tả khi nào dùng và làm từng bước ra sao.
- Tùy chọn: script, template, tài liệu tham khảo đi kèm.

> **Hình dung:** giống tài liệu onboarding cho nhân viên mới. Bạn không nhồi toàn bộ sổ tay vào đầu họ mỗi sáng, mà họ chỉ mở đúng chương khi gặp đúng việc.

## 2. Vấn đề Skill giải quyết

- **Prompt lặp đi lặp lại:** không phải dán lại hướng dẫn dài mỗi lần.
- **Kết quả không nhất quán:** cùng một việc nhưng mỗi lần làm một kiểu.
- **Context phình to:** nhồi mọi hướng dẫn vào prompt làm tốn chỗ và gây nhiễu.
- **Tri thức quy trình nằm trong đầu người:** skill biến "cách chúng ta làm việc này" thành thứ có thể chia sẻ và tái sử dụng.

## 3. Cấu trúc một skill

```
ten-skill/
├── SKILL.md          # Bắt buộc: mô tả + hướng dẫn chính
├── templates/        # Tùy chọn: mẫu file đầu ra
├── scripts/          # Tùy chọn: code agent có thể chạy
└── references/       # Tùy chọn: tài liệu chi tiết, chỉ đọc khi cần
```

Phần đầu của `SKILL.md` thường có **tên** và **mô tả ngắn** nói rõ skill làm gì và khi nào nên dùng. Mô tả này quan trọng nhất vì nó quyết định agent có chọn đúng skill hay không.

## 4. Cách hoạt động: nạp theo nhu cầu (progressive disclosure)

1. Agent luôn thấy **phần mô tả ngắn** của mọi skill đã cài (tốn rất ít context).
2. Khi nhiệm vụ khớp với một mô tả, agent mới **đọc nội dung đầy đủ** của skill đó.
3. Nếu skill có script hoặc tài liệu phụ, agent chỉ mở khi cần ở bước tương ứng.

Nhờ vậy có thể cài rất nhiều skill mà không làm đầy context.

## 5. Ví dụ

**Skill "báo cáo theo mẫu công ty":**

- Mô tả: *"Dùng khi người dùng nhờ viết báo cáo nội bộ."*
- Nội dung: quy tắc định dạng, giọng văn, cấu trúc các mục.
- Kèm: một file template.

Khi bạn nhờ viết báo cáo, agent nhận ra mô tả khớp, nạp skill rồi làm đúng chuẩn công ty.

## 6. Skill vs prompt thông thường

| | Prompt thông thường | Skill |
|---|---|---|
| **Tái sử dụng** | Phải dán lại mỗi lần | Cài một lần, dùng nhiều lần |
| **Chiếm context** | Toàn bộ, ngay từ đầu | Chỉ mô tả ngắn, phần còn lại nạp khi cần |
| **Chia sẻ** | Copy-paste | Chia sẻ cả gói như một thư mục |
| **Kèm công cụ** | Không | Có thể kèm script, template |

## 7. Lưu ý

- **Mô tả mơ hồ thì kích hoạt sai:** quá chung chung thì skill bị gọi nhầm, quá hẹp thì không bao giờ được gọi.
- **Skill viết không rõ thì agent làm không tốt:** chất lượng phụ thuộc vào độ rõ ràng của hướng dẫn.
- **Chỉ cài skill từ nguồn tin cậy:** skill có thể chứa script, nên cần kiểm tra giống như khi cài phần mềm.

## 8. Tự kiểm tra

1. Skill khác gì với việc viết một prompt dài?
2. Vì sao cài nhiều skill mà context vẫn không bị đầy?
3. Phần nào của skill quyết định agent có chọn đúng lúc hay không?

## 9. Chủ đề để đào sâu sau

- Cách viết mô tả để skill kích hoạt đúng lúc
- Kết hợp nhiều skill cho một nhiệm vụ
- Skill chứa script thực thi
- Skill vs MCP: khi nào dùng cái nào (xem [file so sánh](so-sanh-moi-quan-he.md))
