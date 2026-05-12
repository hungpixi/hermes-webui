# Business registration article humanization case

Use this as concrete pattern when a Vietnamese YMYL/legal-tax article already passes SEO checklist but still feels robotic.

## Symptom
User judged article around 82/100: SEO checklist good, but not yet trusted/engaging. Problems:
- Top heavy: many small blocks before article starts (`Câu trả lời nhanh`, decision tree, 3-layer summary, note, TOC, then multiple generic H2s).
- Voice felt like modules assembled for Google, not a trusted resource for solo operators.
- Core sections were correct but lacked concrete lived examples.

## Fix pattern
1. Gọt đầu bài to 5 visible blocks only:
   - human opening with real situation;
   - fast answer;
   - quick classification table;
   - TOC;
   - main article.
2. Remove public stacked H2s such as `Tóm tắt tốt nhất`, `Điểm quan trọng nhất`, `Áp dụng cho ai`, `Việc cần làm ngay`, `Rủi ro nếu hiểu sai`, `Nguồn & ngày kiểm tra` from top. If validator needs labels, update validator or keep labels as non-disruptive compatibility text/comments, not visible H2 stack.
3. Use opening scene:
   - vài job nhỏ / vài đơn Facebook / chuyển khoản cá nhân;
   - then khách quay lại / bảng giá / landing page / hợp đồng / hóa đơn;
   - reframe: not paperwork, but distinguishing personal transaction vs real business model.
4. Rename machine headings to action headings, e.g. `Bảng tình huống chính` → `Bảng tự kiểm tra: trường hợp của bạn đang ở mức nào?`.
5. Add one concrete case story after main table, with timeline, channel, amount range, turning point, and better expert question.
6. Deepen each sign/criterion using fixed mini-format:
   - `Dấu hiệu là gì?`
   - `Ví dụ thực tế`
   - `Rủi ro nếu bỏ qua`
   - `Việc nên làm`
   - `Nguồn nên kiểm tra` where relevant.
7. Reviewer note: avoid public `Người kiểm tra chuyên môn: chưa công bố`. Use: `Ghi chú biên tập: Bài viết là tài liệu tham khảo ban đầu, chưa thay thế tư vấn chuyên môn. Khi có phần kiểm tra độc lập từ kế toán hoặc luật sư, thông tin reviewer sẽ được bổ sung tại đây.`

## Static-site implementation note
If build validator requires old headings, do not let validator force bad UX. Prefer changing validation requirements. If not safe in current session, keep labels outside visible article flow and document that validator should be modernized later.

## Expected effect
This kind of edit improves perceived article quality from publishable/checklist (~82) to more pillar-like (~88) before adding reviewer, primary-source links, and real internal cluster.
