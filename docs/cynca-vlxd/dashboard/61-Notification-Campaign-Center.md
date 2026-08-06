# Cynca VLXD Admin Dashboard — Notification & Campaign Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý chiến dịch thông báo qua Zalo OA, in-app, email, SMS và các kênh được phê duyệt

---

## 1. Mục tiêu

Notification & Campaign Center giúp Cynca VLXD thiết kế, phê duyệt, lập lịch, gửi và đo lường các chiến dịch chăm sóc khách hàng mà không gửi trùng, vượt consent hoặc làm phiền người nhận.

Hệ thống phải trả lời:

- Chiến dịch nào đang ở trạng thái draft, review, scheduled, running hoặc completed?
- Đối tượng nhận là ai và được tạo từ segment nào?
- Kênh nào được phép sử dụng?
- Template nào đang được áp dụng?
- Bao nhiêu tin đã gửi, thành công, thất bại, bị từ chối hoặc opt-out?
- Có vi phạm quiet hours, frequency cap hoặc consent không?

---

## 2. Information architecture

```text
Notification & Campaign Center
├── Tổng quan
├── Campaigns
├── Audience Segments
├── Templates
├── Scheduling
├── Approval Queue
├── Delivery Monitor
├── A/B Tests
├── Suppression & Opt-out
├── Analytics
└── Audit History
```

---

## 3. Permission model

```text
campaigns.center.view
campaigns.view
campaigns.create
campaigns.update
campaigns.approve
campaigns.schedule
campaigns.pause
campaigns.cancel
campaigns.analytics.view
campaigns.templates.manage
campaigns.segments.manage
campaigns.suppression.manage
campaigns.audit.view
```

Các hành động publish, schedule, pause và gửi ra ngoài là quyền nhạy cảm.

---

## 4. Campaign contract

```ts
export type NotificationCampaign = {
  id: string;
  name: string;
  objective: string;
  channel: "zalo_oa" | "in_app" | "email" | "sms";
  status: "draft" | "review" | "approved" | "scheduled" | "running" | "paused" | "completed" | "cancelled" | "failed";
  segmentId: string;
  templateVersionId: string;
  scheduledAt?: string;
  timezone: string;
  ownerUserId: string;
  estimatedAudience?: number;
  frequencyCapPolicyId?: string;
  createdAt: string;
  updatedAt: string;
};
```

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Campaigns đang chạy.
2. Campaigns chờ phê duyệt.
3. Delivery success rate.
4. Failed deliveries.
5. Opt-out rate.
6. Frequency-cap blocks.

Widgets:

- Upcoming campaigns.
- Delivery trend.
- Failed channel summary.
- Top-performing templates.
- Audience overlap warning.
- Consent and suppression alerts.

---

## 6. Campaign workflow

```text
Draft
→ Select audience
→ Select channel/template
→ Validate consent and policy
→ Preview
→ Approval
→ Schedule
→ Send
→ Monitor
→ Review
```

Không được chuyển sang Scheduled khi consent, template, sender hoặc audience validation còn failed/unknown.

---

## 7. Audience segments

Segment có thể dựa trên:

- Branch.
- Customer lifecycle.
- Product category purchased.
- Last order date.
- Repurchase window.
- Service issue history.
- Consent and preferred channel.
- Assigned sales owner.

Không dùng thuộc tính nhạy cảm hoặc inferred traits ngoài policy.

---

## 8. Segment preview

Trước khi gửi hiển thị:

- Estimated recipients.
- Consent-qualified recipients.
- Suppressed recipients.
- Duplicate recipients.
- Branch distribution.
- Channel availability.
- Audience overlap với campaign khác.

Không export toàn bộ PII chỉ để review segment.

---

## 9. Template management

Template gồm:

- Channel.
- Title/subject.
- Body.
- Approved variables.
- Fallback content.
- Locale.
- Version.
- Approval status.

Ví dụ variables:

```text
{{customer_name_masked}}
{{order_code}}
{{product_name}}
{{review_link}}
{{branch_name}}
```

Không cho arbitrary script hoặc HTML không sanitize.

---

## 10. Zalo OA requirements

- Tuân template/message type của provider.
- Kiểm tra user/channel eligibility.
- Lưu provider message ID.
- Theo dõi accepted, delivered, failed và rejected.
- Retry theo error code cho phép.
- Không gửi lại tự động khi kết quả không rõ nếu có nguy cơ duplicate.

---

## 11. Scheduling và quiet hours

- Timezone theo branch hoặc audience policy.
- Hỗ trợ scheduled time và send window.
- Tôn trọng quiet hours.
- Có business-day rules.
- Có deadline cho time-sensitive message.
- Có pause/cancel trước khi gửi.

Không dùng client-side timer để điều phối chiến dịch.

---

## 12. Frequency cap

Ví dụ:

```text
Tối đa 1 tin quảng bá/ngày
Tối đa 3 tin quảng bá/7 ngày
Không giới hạn tin giao dịch bắt buộc theo policy
```

Frequency cap phải áp trên recipient, channel và campaign category.

---

## 13. Consent, opt-out và suppression

Suppression reasons:

- Opt-out.
- Channel invalid.
- Consent denied.
- Complaint/open dispute.
- Frequency cap.
- Internal test exclusion.
- Legal restriction.

Consent `unknown` không được coi là `granted` cho marketing.

---

## 14. A/B testing

Có thể test:

- Subject/title.
- Body copy.
- CTA.
- Send time.

Yêu cầu:

- Random assignment.
- Minimum sample.
- Success metric xác định trước.
- Test duration.
- Winner rule.
- Không thay audience sau khi test bắt đầu.

---

## 15. Delivery monitoring

Theo dõi:

- Queued.
- Accepted by provider.
- Delivered.
- Opened/clicked nếu hợp lệ.
- Failed.
- Rejected.
- Opted out.

Không trình bày open/click như doanh thu nếu chưa có attribution rõ.

---

## 16. Retry và failure handling

Retry cho lỗi tạm thời:

- Timeout.
- Provider unavailable.
- Rate limit.

Không retry vô hạn khi:

- Invalid recipient.
- Consent denied.
- Template rejected.
- Permanent provider error.

Mọi retry phải dùng idempotency key.

---

## 17. Approval safeguards

Campaign cần approval khi:

- Audience lớn.
- Nội dung quảng bá.
- Gửi ngoài giờ thông thường.
- Dùng dữ liệu restricted.
- Có incentive/financial claim.
- Gửi cross-branch.

Preview phải hiển thị audience, content, channel, schedule, opt-out và risk warnings.

---

## 18. Analytics

Metrics:

- Delivery rate.
- Failure rate.
- Open/click rate nếu khả dụng.
- Conversion theo attribution policy.
- Opt-out rate.
- Complaint rate.
- Cost per delivered message.
- Segment performance.

Không gửi message content hoặc PII vào analytics thông thường.

---

## 19. API endpoints đề xuất

```text
GET  /api/v1/campaigns/summary
GET  /api/v1/campaigns
POST /api/v1/campaigns
PATCH /api/v1/campaigns/:id
POST /api/v1/campaigns/:id/validate
POST /api/v1/campaigns/:id/approve
POST /api/v1/campaigns/:id/schedule
POST /api/v1/campaigns/:id/pause
POST /api/v1/campaigns/:id/cancel
GET  /api/v1/campaigns/:id/deliveries
GET  /api/v1/campaign-segments
POST /api/v1/campaign-segments
GET  /api/v1/notification-templates
POST /api/v1/notification-templates
```

---

## 20. Frontend component inventory

```text
NotificationCampaignCenterPage
├── CampaignSummaryCards
├── CampaignTable
├── CampaignBuilder
├── SegmentSelector
├── AudiencePreview
├── TemplateEditor
├── MessagePreview
├── SchedulePanel
├── ApprovalReview
├── DeliveryMonitor
├── ABTestPanel
├── SuppressionPanel
└── CampaignAnalytics
```

---

## 21. Security, privacy và audit

- Backend enforce audience scope và channel permission.
- PII chỉ được dùng trong render service đã kiểm soát.
- Không trả recipient list đầy đủ nếu không cần.
- Signed URL cho export.
- Audit mọi approval, schedule, pause, cancel và suppression change.

Audit events:

```text
campaign.created
campaign.validated
campaign.approved
campaign.scheduled
campaign.started
campaign.paused
campaign.completed
campaign.cancelled
campaign.suppression.updated
```

---

## 22. Testing

### Unit

- Frequency-cap calculation.
- Quiet-hours calculation.
- Consent eligibility.
- Deduplication.
- Retry eligibility.

### Integration

- Consent denied bị loại khỏi audience.
- Duplicate recipient chỉ nhận một lần.
- Scheduled campaign không chạy trước giờ.
- Provider retry dùng idempotency.
- Cross-branch audience bị chặn.

### E2E

- Tạo campaign Zalo OA.
- Chọn segment.
- Preview audience.
- Approve và schedule.
- Theo dõi delivery.
- Pause campaign.
- Xem analytics và opt-out.

---

## 23. Acceptance checklist

- [ ] Có Campaign workflow đầy đủ.
- [ ] Có audience segment và preview.
- [ ] Có template versioning.
- [ ] Có Zalo OA, in-app, email và SMS theo policy.
- [ ] Có scheduling, quiet hours và frequency cap.
- [ ] Có consent, opt-out và suppression.
- [ ] Có A/B testing.
- [ ] Có delivery monitor và retry an toàn.
- [ ] Có approval, audit, analytics và testing.
- [ ] Có responsive và accessibility states.

---

## 24. Những điều không được làm

- Không gửi khi consent chưa hợp lệ.
- Không gửi trùng do thiếu idempotency.
- Không vượt quiet hours hoặc frequency cap.
- Không retry lỗi permanent vô hạn.
- Không dùng feature flag thay permission.
- Không hiển thị recipient PII ngoài quyền.
- Không coi click/open là doanh thu mặc định.

---

## 25. Kết luận

Notification & Campaign Center của Cynca VLXD phải giúp doanh nghiệp chăm sóc khách hàng đúng lúc, đúng kênh và đúng quyền. Mọi campaign phải có audience validation, consent, suppression, frequency cap, approval, delivery monitoring và audit đầy đủ.
