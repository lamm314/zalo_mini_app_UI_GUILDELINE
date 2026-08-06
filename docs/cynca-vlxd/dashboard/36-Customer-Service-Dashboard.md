# Cynca VLXD Admin Dashboard — Customer Service Dashboard Specification

> Phiên bản: 1.0  
> Phạm vi: Dashboard chăm sóc khách hàng dành cho quản lý CSKH và nhân viên hỗ trợ  
> Đối tượng sử dụng: Product, Design, Frontend, Backend, Customer Service, QA, Security và AI coding agent  
> Tài liệu liên quan: `08-Orders-Table.md`, `16-Data-Contracts.md`, `23-Security-Privacy.md`, `27-Role-Based-Dashboard.md`, `30-Notification-Center.md`, `31-Search-Command-Center.md`

---

## 1. Mục tiêu

Customer Service Dashboard giúp đội CSKH Cynca VLXD theo dõi yêu cầu hỗ trợ, khiếu nại, đơn giao thất bại, khách cần gọi lại và các tác vụ sắp quá hạn.

Dashboard phải trả lời nhanh:

- Có bao nhiêu yêu cầu mới, đang xử lý và quá SLA?
- Khiếu nại nào có mức độ nghiêm trọng cao?
- Đơn hàng nào giao thất bại hoặc cần liên hệ khách?
- Khách hàng nào đang chờ phản hồi?
- Nhân viên hoặc nhóm nào đang quá tải?
- Chủ đề khiếu nại nào đang tăng bất thường?
- Chất lượng phản hồi và mức độ hài lòng đang thay đổi ra sao?
- Việc gì cần ưu tiên xử lý ngay?

Customer Service Dashboard không thay thế hệ thống ticket, CRM hoặc trang chi tiết đơn hàng. Đây là màn hình tổng hợp để điều phối và xử lý nhanh.

---

## 2. Hai biến thể chính

### Customer Service Manager Dashboard

Trọng tâm:

- Tổng quan toàn đội.
- SLA và backlog.
- Phân bổ tải công việc.
- Khiếu nại nghiêm trọng.
- Xu hướng chủ đề hỗ trợ.
- Chất lượng dịch vụ.

### Customer Service Agent Dashboard

Trọng tâm:

- Ticket được giao.
- Khách cần gọi lại.
- Đơn giao thất bại.
- Ticket gần quá SLA.
- Tác vụ cá nhân hôm nay.
- Lịch sử xử lý gần nhất.

Hai biến thể dùng cùng component registry, khác permission, scope và thứ tự widget.

---

## 3. Nguyên tắc thiết kế

1. Ưu tiên việc cần hành động trước chart thứ cấp.
2. Ticket quá SLA và khiếu nại nghiêm trọng phải nổi bật.
3. Không hiển thị PII dư thừa trong danh sách tổng hợp.
4. Không dùng `đã đọc` thay cho `đã xử lý`.
5. Không tự đóng ticket khi người dùng chỉ mở detail.
6. Mọi action phải dựa trên `availableActions` từ backend.
7. Không hiển thị dữ liệu ngoài team/branch/assigned scope.
8. Không dùng số `0` khi dữ liệu chưa tải hoặc API lỗi.
9. Không để một widget lỗi làm sập toàn Dashboard.
10. Dashboard phải dùng tốt trên laptop và tablet.

---

## 4. Layout — Customer Service Manager

Desktop 1440px:

```text
Page Toolbar
Service Health Summary
KPI Grid 4–6 cards
Ticket Volume & SLA Trend         8 columns
Priority Queue                    4 columns
Team Workload                     7 columns
Complaint Topics                  5 columns
Failed Delivery Follow-up         6 columns
Customer Satisfaction             6 columns
Recent Escalations               12 columns
```

---

## 5. Layout — Customer Service Agent

Desktop 1440px:

```text
Page Toolbar
My Shift Summary
KPI Grid 4 cards
My Priority Queue                 7 columns
Customers to Call Back            5 columns
Failed Delivery Cases             6 columns
My Ticket Trend                    6 columns
Recent Customer Activity         12 columns
```

Agent View cần nhiều queue và quick action hơn chart tổng hợp.

---

## 6. Page toolbar

### Manager

- Date range.
- Branch filter.
- Team/agent filter.
- Channel filter.
- Priority filter.
- SLA status.
- Export service report.
- Refresh.

### Agent

- Scope `Của tôi`.
- Status filter.
- Channel filter.
- Global search.
- Create ticket nếu có quyền.
- Refresh.

Không hiển thị team filter cho agent không có quyền xem người khác.

---

## 7. KPI — Manager

KPI đề xuất:

1. Ticket mới.
2. Ticket đang mở.
3. Ticket quá SLA.
4. First Response Time trung vị.
5. Resolution Time trung vị.
6. CSAT hoặc tỷ lệ hài lòng nếu có dữ liệu.

KPI tùy chọn:

- Escalation rate.
- Reopen rate.
- Failed delivery cases.
- Complaint rate trên 100 đơn.
- Backlog trên 24 giờ.

---

## 8. KPI — Agent

KPI đề xuất:

1. Ticket được giao.
2. Ticket cần phản hồi.
3. Ticket gần quá SLA.
4. Khách cần gọi lại.

Tùy chọn:

- Ticket đã xử lý hôm nay.
- First Response Time cá nhân.
- Reopened tickets.

Không hiển thị xếp hạng cá nhân toàn đội nếu policy không cho phép.

---

## 9. Service Health Summary

Tóm tắt theo các nhóm:

```text
Ticket volume
Backlog
SLA
Escalations
Failed delivery
Customer satisfaction
```

Mỗi nhóm dùng trạng thái:

```text
Tốt
Cần chú ý
Rủi ro
Chưa đủ dữ liệu
```

Phải có label và lý do, không chỉ dùng màu.

---

## 10. Ticket state model

```ts
export type SupportTicketStatus =
  | "new"
  | "open"
  | "pending_customer"
  | "pending_internal"
  | "resolved"
  | "closed"
  | "reopened"
  | "cancelled";
```

Phân biệt:

- `resolved`: vấn đề đã được giải quyết, có thể chờ xác nhận.
- `closed`: ticket đã đóng theo policy.
- `pending_customer`: chờ khách phản hồi.
- `pending_internal`: chờ bộ phận nội bộ.

Không dùng một trạng thái `done` chung cho mọi trường hợp.

---

## 11. Priority model

```ts
export type SupportPriority =
  | "urgent"
  | "high"
  | "normal"
  | "low";
```

Ví dụ:

- Urgent: rủi ro an toàn, thanh toán lớn, khách hàng chiến lược, sự cố diện rộng.
- High: đơn giao thất bại, khiếu nại nghiêm trọng, ticket sắp quá SLA.
- Normal: yêu cầu thông thường.
- Low: thông tin hoặc tư vấn không khẩn cấp.

Business rule phải ở backend hoặc service layer, không hardcode trong JSX.

---

## 12. SLA model

Các mốc có thể gồm:

```text
First response SLA
Next response SLA
Resolution SLA
Callback SLA
Escalation SLA
```

Ví dụ tham khảo:

```text
Urgent: phản hồi đầu trong 15 phút
High: phản hồi đầu trong 30 phút
Normal: phản hồi đầu trong 2 giờ
Low: phản hồi đầu trong 8 giờ
```

SLA thực tế phải cấu hình theo priority, channel, branch hoặc customer segment nếu cần.

---

## 13. Ticket Volume & SLA Trend

Chart đề xuất:

- Ticket created.
- Ticket resolved.
- Backlog.
- SLA breach rate.

Controls:

- Day/week/month.
- Channel.
- Priority.
- Team.

Tooltip:

- Số ticket.
- Tỷ lệ SLA.
- Median response time.
- Median resolution time.

Không dùng average duy nhất nếu outlier lớn; ưu tiên median hoặc percentile.

---

## 14. Priority Queue

Queue dành cho manager và agent.

Mỗi item gồm:

- Ticket code.
- Customer summary.
- Priority.
- Channel.
- Subject.
- Current status.
- SLA countdown.
- Assigned agent.
- Last interaction.
- Primary action.

Thứ tự mặc định:

1. Urgent quá SLA.
2. Urgent gần quá SLA.
3. High quá SLA.
4. Các ticket còn lại theo SLA deadline.

---

## 15. Agent Priority Queue

Agent chỉ thấy:

- Ticket assigned.
- Ticket được phép nhận từ shared queue.
- Callback task assigned.
- Failed-delivery follow-up assigned.

Quick actions:

- Mở ticket.
- Trả lời.
- Gọi lại.
- Chuyển trạng thái.
- Escalate.
- Reassign nếu có quyền.

Không cho agent xem toàn bộ PII trước khi mở ticket nếu không cần.

---

## 16. Team Workload

Dành cho manager.

Metrics:

- Open tickets.
- Urgent/high tickets.
- Tickets near SLA.
- Average active load.
- Oldest ticket age.
- Availability/shift status nếu có.

Không chỉ xếp hạng theo số ticket vì độ phức tạp khác nhau.

Có thể hiển thị workload score có giải thích rõ.

---

## 17. Workload balancing

Manager có thể:

- Xem agent quá tải.
- Reassign ticket.
- Chuyển shared queue.
- Xem skill/language/channel phù hợp.

Reassignment cần:

- Permission.
- Backend confirmation.
- Audit event.
- Notification tới agent mới.

Không optimistic update nếu assignment ảnh hưởng workflow quan trọng.

---

## 18. Complaint Topics

Taxonomy đề xuất:

```text
Product quality
Wrong item
Missing item
Delivery delay
Delivery damage
Payment issue
Refund issue
Warranty
Pricing
Staff attitude
Other
```

Widget hiển thị:

- Top complaint topics.
- Trend.
- Severity mix.
- Resolution rate.
- Average resolution time.

Không dựa hoàn toàn vào free-text topic nếu taxonomy chưa chuẩn hóa.

---

## 19. Complaint trend detection

Có thể cảnh báo khi:

- Chủ đề tăng vượt baseline.
- Một sản phẩm có nhiều khiếu nại tương tự.
- Một chi nhánh có tỷ lệ khiếu nại cao.
- Delivery damage tăng theo nhà vận chuyển.

Nếu dùng mô hình bất thường, phải có confidence và nguồn dữ liệu rõ.

---

## 20. Failed Delivery Follow-up

Danh sách đơn giao thất bại hoặc giao không thành công.

Mỗi item:

- Order code.
- Customer summary đã mask.
- Failure reason.
- Attempt count.
- Carrier.
- Last attempt.
- Next action.
- Assigned agent.
- SLA.

Quick actions:

- Gọi khách.
- Cập nhật thông tin giao hàng.
- Hẹn giao lại.
- Chuyển cho vận hành.
- Hủy theo workflow nếu có quyền.

---

## 21. Callback Queue

Các lý do:

- Khách yêu cầu gọi lại.
- Báo giá chưa phản hồi.
- Khiếu nại cần xác minh.
- Giao hàng thất bại.
- Sau bán hàng.
- Yêu cầu đổi/trả.

Mỗi item:

- Customer.
- Reason.
- Due time.
- Last contact.
- Related order/ticket.
- Agent.
- Status.

Không lưu raw call content trong analytics.

---

## 22. Customer Satisfaction

Chỉ hiển thị nếu có dữ liệu đáng tin cậy.

Metrics:

- CSAT.
- Survey response rate.
- Positive/neutral/negative distribution.
- Trend.
- Top dissatisfaction reasons.

Phải hiển thị sample size.

Không dùng điểm CSAT khi số phản hồi quá thấp mà thiếu cảnh báo.

---

## 23. NPS hoặc CES

Nếu có NPS hoặc Customer Effort Score:

- Ghi rõ định nghĩa.
- Ghi rõ sample size.
- Ghi rõ kỳ khảo sát.
- Không trộn CSAT, NPS và CES thành một chỉ số chung.

Không dựng chỉ số nếu hệ thống chưa thu thập đúng phương pháp.

---

## 24. Escalation Center

Các loại escalation:

```text
manager_review
warehouse_issue
finance_issue
product_quality
delivery_partner
security_issue
legal_issue
```

Mỗi escalation gồm:

- Ticket.
- Reason.
- Severity.
- Current owner.
- Escalated to.
- Age.
- Status.
- Next action.

Security hoặc legal escalation phải tuân permission riêng.

---

## 25. Reopened Tickets

Widget giúp phát hiện chất lượng xử lý chưa tốt.

Hiển thị:

- Reopen rate.
- Top topics.
- Agent/team.
- Time to reopen.
- Resolution count.

Không dùng reopen rate để đánh giá cá nhân thiếu ngữ cảnh.

---

## 26. Contact Channel Breakdown

Các kênh:

- Zalo OA.
- Zalo Mini App.
- Hotline.
- Email.
- Website.
- Social.
- In-store.
- Internal referral.

Hiển thị:

- Ticket volume.
- SLA.
- Resolution rate.
- CSAT.

Không hiển thị kênh nếu attribution chưa đáng tin cậy.

---

## 27. Agent Activity Summary

Dành cho agent:

- Ticket đã phản hồi.
- Ticket đã giải quyết.
- Callback completed.
- Escalations created.
- Pending customer.
- Reopened tickets.

Không biến thành bảng xếp hạng cạnh tranh nếu policy không yêu cầu.

---

## 28. Recent Customer Activity

Bảng gồm:

- Activity type.
- Ticket/order/customer reference.
- Summary.
- Agent.
- Timestamp.
- Result.

Activity types:

```text
message_sent
call_completed
callback_scheduled
ticket_reassigned
ticket_escalated
ticket_resolved
refund_requested
address_updated
```

Không hiển thị nội dung tin nhắn đầy đủ trong bảng tổng hợp.

---

## 29. Ticket detail drawer

Khi mở ticket:

- Ticket summary.
- Customer summary đã permission-filtered.
- Related order.
- Conversation timeline.
- Internal notes.
- Attachments.
- SLA status.
- Available actions.
- Escalation history.

Drawer phải:

- Quản lý focus.
- Không tải PII ngoài quyền.
- Không tự mark resolved.
- Hỗ trợ loading/error riêng.

---

## 30. Internal notes

Internal notes:

- Chỉ người nội bộ có quyền mới xem.
- Không gửi tới khách.
- Có author và timestamp.
- Có audit nếu sửa/xóa.
- Không chứa secret hoặc dữ liệu nhạy cảm không cần thiết.

Nếu notes hỗ trợ rich text, phải sanitize.

---

## 31. Attachments

Yêu cầu:

- MIME/type whitelist.
- Giới hạn dung lượng.
- Malware scanning nếu có.
- Signed URL có thời hạn.
- Preview an toàn.
- Không render HTML file trực tiếp.
- Permission check khi download.

---

## 32. Customer data masking

Ví dụ:

| Dữ liệu | Agent xử lý | Manager | Viewer |
|---|---|---|---|
| Tên khách | Có | Có | Có thể mask |
| Số điện thoại | Full khi cần | Mask hoặc full theo quyền | Mask |
| Email | Full khi cần | Mask hoặc full | Mask |
| Địa chỉ | Chỉ khi liên quan giao hàng | Hạn chế | Ẩn |
| Payment detail | Hạn chế | Hạn chế | Ẩn |

Masking nên được backend áp dụng trước khi trả dữ liệu.

---

## 33. Customer service task contract

```ts
export type CustomerServiceTask = {
  id: string;
  type: "reply" | "callback" | "follow_up" | "escalation" | "failed_delivery" | "refund_follow_up";
  title: string;
  priority: "urgent" | "high" | "normal" | "low";
  ticketId?: string;
  orderId?: string;
  customerId?: string;
  assignedUserId: string;
  dueAt?: string;
  status: "open" | "in_progress" | "completed" | "dismissed";
  availableActions: string[];
};
```

---

## 34. Support ticket contract

```ts
export type SupportTicketSummary = {
  id: string;
  code: string;
  subject: string;
  category: string;
  priority: SupportPriority;
  status: SupportTicketStatus;
  channel: string;
  customerId: string;
  customerName: string;
  maskedPhone?: string;
  orderId?: string;
  assignedUserId?: string;
  teamId?: string;
  firstResponseDueAt?: string;
  resolutionDueAt?: string;
  lastInteractionAt?: string;
  createdAt: string;
  updatedAt: string;
  availableActions: string[];
};
```

---

## 35. Customer Service Dashboard contract

```ts
export type CustomerServiceDashboardData = {
  scope: DashboardScope;
  health: CustomerServiceHealthSummary;
  kpis: DashboardKPI[];
  ticketTrend: SupportTrendData;
  priorityQueue: SupportTicketSummary[];
  workload?: AgentWorkloadSummary[];
  complaintTopics: ComplaintTopicSummary[];
  failedDeliveries: FailedDeliveryCase[];
  callbacks: CustomerServiceTask[];
  satisfaction?: SatisfactionSummary;
  escalations: EscalationSummary[];
  recentActivity: CustomerServiceActivity[];
  updatedAt: string;
};
```

---

## 36. Permissions

Đề xuất:

```text
support.dashboard.view
support.tickets.view
support.tickets.view_all
support.tickets.update
support.tickets.assign
support.tickets.escalate
support.internal_notes.view
support.internal_notes.update
support.attachments.view
support.attachments.upload
support.workload.view
support.satisfaction.view
customers.view
customers.view_pii
orders.view
reports.support.export
```

Không dựa duy nhất vào role name `support`, `manager` hoặc `admin`.

---

## 37. Scope behavior

### Manager

Có thể gồm:

```text
branch
team
channel
all assigned agents
```

### Agent

Mặc định:

```text
assigned
self
shared queue được phép
```

Filter không được mở rộng ngoài backend scope.

---

## 38. Search integration

CSKH Search ưu tiên:

1. Ticket code.
2. Order code.
3. Customer/company theo quyền.
4. Phone/email với minimum query length và permission.
5. Complaint topic.

Không log raw query.

Không lưu phone/email query trong recent searches.

---

## 39. Notification integration

Notification categories:

- Ticket assigned.
- Ticket near SLA.
- Ticket breached SLA.
- Customer replied.
- Failed delivery case.
- Escalation returned.
- Refund status changed.
- Complaint spike.

Không toast mọi customer reply; ưu tiên Notification Center và Priority Queue.

---

## 40. Real-time updates

Events đề xuất:

```text
support.ticket.created
support.ticket.updated
support.ticket.assigned
support.customer.replied
support.ticket.escalated
support.sla.breached
support.callback.due
```

Frontend phải:

- Validate schema.
- Deduplicate event.
- Update cache có chọn lọc.
- Không tự mở ticket.
- Không tự mark read/resolved.
- Không phát toast hàng loạt.

---

## 41. Loading states

- KPI skeleton.
- Ticket trend placeholder.
- Queue skeleton.
- Workload skeleton.
- Satisfaction skeleton.

Không hiển thị `0` khi chưa tải.

---

## 42. Empty states

### Không có ticket ưu tiên

```text
Không có ticket ưu tiên đang chờ xử lý.
```

### Không có callback

```text
Không có khách hàng nào cần gọi lại trong thời điểm này.
```

### Không có escalation

```text
Không có trường hợp nào đang được escalated.
```

### Chưa có satisfaction data

```text
Chưa có đủ phản hồi để tính chỉ số hài lòng.
```

---

## 43. Error states

- Widget lỗi độc lập.
- Satisfaction API lỗi không làm mất Priority Queue.
- Ticket API lỗi phải hiển thị `Chưa thể tải danh sách hỗ trợ`.
- Có retry và request ID.
- Không thay dữ liệu lỗi bằng danh sách rỗng giả.

---

## 44. Stale data

Ngưỡng tham khảo:

| Dataset | Stale threshold |
|---|---:|
| Ticket queue | 1–3 phút |
| Customer replies | Gần real-time |
| SLA status | 1 phút |
| Failed delivery cases | 5–15 phút |
| Satisfaction metrics | 30–60 phút |
| Complaint trends | 30–60 phút |

SLA stale phải hiển thị rõ vì ảnh hưởng ưu tiên xử lý.

---

## 45. Responsive behavior

### ≥1440px

- KPI 4–6 cột.
- Trend/Queue 8/4.
- Workload/Topics 7/5.

### 1024–1439px

- KPI 2–3 cột.
- Widgets xếp dọc khi cần.
- Tables/list scroll riêng.

### 768–1023px

- Sidebar drawer.
- KPI 2 cột.
- Queue một cột.
- Touch targets 44px.

### <768px

Agent mobile fallback ưu tiên:

1. My Priority Queue.
2. SLA alerts.
3. Callbacks.
4. Failed delivery cases.
5. Recent activity.

---

## 46. Accessibility

- KPI có accessible summary.
- Priority và SLA không chỉ dùng màu.
- Queue dùng semantic list/table.
- Countdown có text dễ hiểu.
- Drawer/dialog quản lý focus.
- Conversation timeline có heading hợp lý.
- Touch target tối thiểu 44px.
- Zoom 200% không mất action chính.
- Live updates dùng `aria-live=polite` phù hợp.

---

## 47. Security và privacy

- Backend enforce assigned/team/branch scope.
- Không trả toàn bộ customer data rồi lọc ở client.
- PII masking theo role.
- Internal notes và attachments có permission riêng.
- Signed URL có thời hạn.
- Không ghi conversation content vào analytics.
- Cache key gồm user, permission, team, branch và filters.
- Clear cache nhạy cảm khi logout.

---

## 48. Audit requirements

Bắt buộc hoặc khuyến nghị mạnh:

```text
support.ticket.assigned
support.ticket.reassigned
support.ticket.escalated
support.ticket.resolved
support.ticket.closed
support.internal_note.created
support.attachment.uploaded
support.customer_data.viewed_sensitive
```

Audit gồm actor, entity, action, before/after nếu phù hợp, reason, result, request ID và timestamp.

---

## 49. Analytics events

```text
support.dashboard.viewed
support.filter.applied
support.ticket.opened
support.reply.started
support.callback.started
support.ticket.escalated
support.ticket.resolved
support.report.export_requested
```

Không gửi customer name, phone, email, ticket text hoặc conversation content vào analytics.

---

## 50. Observability

Theo dõi:

- Dashboard load success.
- Ticket API latency.
- SLA freshness.
- Assignment mutation failure.
- Reply/send failure.
- Escalation failure.
- Real-time disconnect.
- Duplicate event rate.
- Permission denied anomalies.

---

## 51. Testing

### Unit

- SLA breach calculation.
- Priority sorting.
- Ticket state mapping.
- Workload score mapping.
- PII masking display rules.
- Complaint trend aggregation.

### Component

- KPI states.
- Priority Queue.
- Workload widget.
- Failed Delivery Follow-up.
- Callback Queue.
- Satisfaction card.
- Ticket detail drawer.

### Integration

- Agent chỉ thấy assigned tickets.
- Manager filter agent/team.
- Reassignment conflict handling.
- Realtime customer reply update.
- SLA breach ordering.
- Export đúng scope.

### E2E

- Manager mở Dashboard.
- Chọn agent.
- Mở urgent ticket.
- Reassign ticket.
- Agent trả lời khách.
- Schedule callback.
- Escalate case.
- Resolve ticket.
- Export support report.

---

## 52. Visual QA

Baseline:

```text
Customer Service Manager — 1440 × 900
Customer Service Agent — 1440 × 900
Manager — 1024 × 768
Agent Mobile Fallback
Urgent SLA Breach
No Priority Tickets
No Satisfaction Data
Failed Delivery Queue
Long Vietnamese Subject
Ticket Detail Drawer
Permission Limited
```

Kiểm tra:

- Priority hierarchy.
- SLA visibility.
- Long text truncation.
- Workload readability.
- PII masking.
- Focus states.
- Mobile touch targets.

---

## 53. Performance budgets

Mục tiêu tham khảo:

```text
Dashboard LCP p75 < 2,5s
Support overview API p95 < 1,5s
Queue refresh < 1s trong điều kiện bình thường
Reply mutation feedback < 1,5s
Search p95 < 1s
```

Virtualize danh sách dài và giới hạn số ticket trên Dashboard.

---

## 54. AI-assisted reply requirements

Nếu dùng AI gợi ý trả lời:

- Chỉ dùng dữ liệu đã permission-filtered.
- Không gửi PII hoặc full conversation tới provider chưa phê duyệt.
- Không tự gửi tin nhắn.
- Agent phải review và chủ động gửi.
- Hiển thị `Gợi ý được tạo tự động`.
- Có logging an toàn và quality review.
- Không dùng AI để tự đóng hoặc tự escalate ticket.

---

## 55. AI coding agent requirements

AI coding agent phải:

- Phân biệt Manager và Agent scope.
- Không hardcode SLA, priority hoặc complaint rules thiếu nguồn.
- Không trả PII ngoài permission.
- Không mark ticket resolved chỉ vì mở hoặc đọc.
- Không optimistic update assignment/escalation quan trọng thiếu conflict handling.
- Không gửi conversation content vào analytics/logs.
- Bổ sung test cho SLA, assigned scope, realtime deduplication và PII masking.
- Báo rõ API, permissions, scope và backend assumptions.

---

## 56. Acceptance checklist

- [ ] Có biến thể Manager và Agent.
- [ ] Toolbar đúng theo quyền và scope.
- [ ] KPI dịch vụ rõ ràng.
- [ ] Có Service Health Summary.
- [ ] Có Ticket Volume & SLA Trend.
- [ ] Có Priority Queue.
- [ ] Có Team Workload cho manager.
- [ ] Có Complaint Topics.
- [ ] Có Failed Delivery Follow-up.
- [ ] Có Callback Queue.
- [ ] Có Satisfaction và Escalation Center theo dữ liệu/quyền.
- [ ] Có Ticket Detail Drawer.
- [ ] Có internal notes và attachments an toàn.
- [ ] Search và Notification integration đầy đủ.
- [ ] Permissions và assigned/team/branch scope được backend enforce.
- [ ] Loading, empty, error và stale states đầy đủ.
- [ ] Responsive và accessibility hoàn chỉnh.
- [ ] Analytics, observability, security và audit được xác định.
- [ ] Unit, component, integration, E2E và Visual QA đầy đủ.

---

## 57. Những điều không được làm

- Không hiển thị toàn bộ ticket cho agent thiếu quyền.
- Không đồng nhất read với resolved.
- Không tự đóng ticket khi mở detail.
- Không hiển thị PII dư thừa.
- Không toast mọi customer reply.
- Không hardcode SLA hoặc complaint rules không có nguồn.
- Không dùng average duy nhất khi outlier lớn.
- Không dùng CSAT khi sample quá nhỏ thiếu cảnh báo.
- Không optimistic update reassignment hoặc escalation thiếu kiểm soát.
- Không thay CRM/ticketing system bằng Dashboard tổng hợp.

---

## 58. Kết luận

Customer Service Dashboard của Cynca VLXD phải giúp quản lý nhìn thấy sức khỏe dịch vụ và giúp agent biết ngay trường hợp cần xử lý tiếp theo. Giao diện cần ưu tiên SLA, priority, workload và quyền riêng tư, đồng thời giữ chặt scope, PII masking, audit và tính chính xác của trạng thái ticket.

File tiếp theo đề xuất:

```text
README.md
37-Branch-Manager-Dashboard.md
```