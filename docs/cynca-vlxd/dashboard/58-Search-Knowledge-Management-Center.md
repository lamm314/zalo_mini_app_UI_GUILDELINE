# Cynca VLXD Admin Dashboard — Search & Knowledge Management Center Specification

> Phiên bản: 1.0  
> Phạm vi: Quản lý tìm kiếm nội bộ, chỉ mục, tài liệu nghiệp vụ, taxonomy và chất lượng kết quả  
> Đối tượng sử dụng: Product, Operations, Content, Data, Engineering, Security, QA và AI coding agent

---

## 1. Mục tiêu

Search & Knowledge Management Center giúp Cynca VLXD quản lý khả năng tìm kiếm xuyên suốt Dashboard và kho kiến thức nội bộ, bảo đảm người dùng tìm đúng dữ liệu, đúng tài liệu và đúng phạm vi quyền.

Hệ thống phải trả lời:

- Những nguồn nào đang được lập chỉ mục?
- Kết quả tìm kiếm có đúng, đủ và cập nhật không?
- Từ khóa nào không có kết quả hoặc trả kết quả kém?
- Tài liệu nào đã cũ, trùng lặp hoặc thiếu owner?
- Quyền truy cập có được áp dụng trước khi trả kết quả không?
- Có synonym, taxonomy hoặc ranking rule nào cần điều chỉnh?
- Index nào đang lỗi, stale hoặc cần reindex?

---

## 2. Information architecture

```text
Search & Knowledge Management
├── Tổng quan
├── Search Sources
├── Index Health
├── Search Analytics
├── Ranking & Synonyms
├── Knowledge Library
├── Taxonomy & Tags
├── Content Review Queue
├── Reindex Jobs
└── Audit History
```

---

## 3. Permission model

```text
search.center.view
search.sources.view
search.sources.manage
search.indexes.view
search.indexes.manage
search.analytics.view
search.ranking.manage
knowledge.library.view
knowledge.library.manage
knowledge.taxonomy.manage
knowledge.review.manage
search.reindex.run
search.audit.view
```

---

## 4. Search source contract

```ts
export type SearchSource = {
  id: string;
  key: string;
  name: string;
  type: "database" | "api" | "document_collection" | "knowledge_base" | "file_store";
  entityTypes: string[];
  classification: "public" | "internal" | "confidential" | "restricted";
  permissionPolicyKey: string;
  freshnessSlaMinutes?: number;
  ownerUserId: string;
  status: "active" | "paused" | "error" | "deprecated";
  lastIndexedAt?: string;
};
```

Không trả toàn bộ kết quả rồi mới lọc quyền ở client.

---

## 5. Dashboard tổng quan

KPI đề xuất:

1. Search success rate.
2. Zero-result rate.
3. Indexes healthy.
4. Sources stale.
5. Knowledge articles cần review.
6. Reindex jobs lỗi.

Widgets:

- Search volume trend.
- Top zero-result queries đã chuẩn hóa.
- Slow searches.
- Index freshness.
- Content gaps.
- Popular knowledge topics.

---

## 6. Index status model

```ts
export type SearchIndexStatus =
  | "healthy"
  | "indexing"
  | "degraded"
  | "stale"
  | "failed"
  | "paused"
  | "unknown";
```

Không hiển thị `healthy` khi index telemetry lỗi.

---

## 7. Index health

Theo dõi:

- Document count.
- Last successful indexing.
- Index lag.
- Failed documents.
- Schema version.
- Storage size.
- Query latency.
- Permission-filter errors.

Mỗi index cần owner và runbook.

---

## 8. Search analytics

Metrics:

- Query volume.
- Click-through rate.
- Search success rate.
- Zero-result rate.
- Reformulation rate.
- Time to first click.
- Abandonment rate.
- Top result click share.

Không log raw query chứa PII, order code hoặc secret nếu không được phép.

---

## 9. Query privacy

- Redact phone, email, token và identifier nhạy cảm.
- Chỉ lưu normalized query khi policy cho phép.
- Có retention riêng.
- Không dùng search analytics để giám sát cá nhân thiếu mục đích hợp lệ.
- Restricted query review cần permission đặc biệt.

---

## 10. Search result contract

```ts
export type SearchResultItem = {
  id: string;
  entityType: string;
  title: string;
  subtitle?: string;
  snippet?: string;
  status?: string;
  sourceId: string;
  score?: number;
  updatedAt?: string;
  href?: string;
  availableActions?: string[];
};
```

Snippet phải sanitize và mask dữ liệu nhạy cảm.

---

## 11. Ranking configuration

Ranking có thể dựa trên:

- Text relevance.
- Exact code match.
- Recency.
- Status priority.
- User role.
- Branch scope.
- Business importance.
- Popularity có giới hạn.

Không dùng popularity để đẩy dữ liệu ngoài scope hoặc nội dung cũ lên đầu.

---

## 12. Synonyms và aliases

Ví dụ:

```text
VLXD ↔ vật liệu xây dựng
xi măng ↔ cement
đơn ↔ đơn hàng
kho ↔ warehouse
```

Mỗi synonym set cần:

- Domain.
- Locale.
- Owner.
- Status.
- Version.
- Test queries.

Không tự thêm synonym production thiếu review.

---

## 13. Typo tolerance

Hỗ trợ có kiểm soát:

- Thiếu dấu tiếng Việt.
- Sai một ký tự.
- Khoảng trắng thừa.
- Viết liền/tách.
- Alias thương hiệu.

Không áp fuzzy matching mạnh cho mã đơn, SKU hoặc mã tài chính.

---

## 14. Search facets

Facets có thể gồm:

- Entity type.
- Status.
- Branch.
- Warehouse.
- Date.
- Category.
- Owner.

Facet values phải được backend lọc theo permission và scope.

---

## 15. Knowledge article contract

```ts
export type KnowledgeArticle = {
  id: string;
  slug: string;
  title: string;
  summary: string;
  categoryId: string;
  tags: string[];
  requiredPermissions?: string[];
  relatedRoutes?: string[];
  ownerUserId: string;
  reviewerUserId?: string;
  version: number;
  status: "draft" | "review" | "published" | "archived";
  lastReviewedAt?: string;
  nextReviewAt?: string;
  updatedAt: string;
};
```

---

## 16. Knowledge lifecycle

```text
Draft
→ Review
→ Approved
→ Published
→ Periodic Review
→ Update / Archive
```

Không để bài viết published không có owner hoặc review date.

---

## 17. Taxonomy

Taxonomy gồm:

- Categories.
- Tags.
- Product/module relationships.
- Role relevance.
- Error-code mapping.
- Related workflows.

Không tạo tag tự do không kiểm soát dẫn đến trùng lặp và sai chính tả.

---

## 18. Content quality checks

Kiểm tra:

- Broken links.
- Missing owner.
- Stale screenshots.
- Deprecated feature references.
- Duplicate articles.
- Missing permissions.
- Missing headings/alt text.
- Readability.

Content check lỗi không tự archive bài viết mà thiếu review.

---

## 19. Content review queue

Queue hiển thị:

- Article.
- Reason review.
- Owner.
- Last updated.
- Usage.
- Related feature release.
- Due date.
- Status.

Ưu tiên bài viết liên quan chức năng critical hoặc có traffic cao.

---

## 20. Reindex workflow

```text
Select source/index
→ Choose full or incremental
→ Estimate impact
→ Validate capacity
→ Run
→ Monitor progress
→ Verify document counts and sample queries
→ Complete
```

Không chạy full reindex production thiếu capacity check nếu có thể ảnh hưởng tìm kiếm.

---

## 21. Reindex job contract

```ts
export type ReindexJob = {
  id: string;
  indexId: string;
  type: "full" | "incremental" | "repair";
  status: "queued" | "running" | "verifying" | "completed" | "failed" | "cancelled";
  processedDocuments?: number;
  failedDocuments?: number;
  startedAt?: string;
  completedAt?: string;
  initiatedBy: string;
};
```

---

## 22. Search quality evaluation

Evaluation suite gồm:

- Query.
- Expected entity types.
- Expected top results.
- Prohibited results.
- Permission context.
- Locale.

Không publish ranking/synonym changes nếu critical queries bị regression.

---

## 23. API endpoints đề xuất

```text
GET  /api/v1/search-management/summary
GET  /api/v1/search-management/sources
GET  /api/v1/search-management/indexes
GET  /api/v1/search-management/analytics
GET  /api/v1/search-management/synonyms
POST /api/v1/search-management/synonyms
POST /api/v1/search-management/evaluations/run
POST /api/v1/search-management/indexes/:id/reindex
GET  /api/v1/knowledge/articles
POST /api/v1/knowledge/articles
PATCH /api/v1/knowledge/articles/:id
GET  /api/v1/knowledge/review-queue
```

---

## 24. Frontend component inventory

```text
SearchKnowledgeManagementCenterPage
├── SearchSummaryCards
├── SearchSourceTable
├── IndexHealthGrid
├── SearchAnalyticsPanel
├── ZeroResultQueryTable
├── RankingRuleEditor
├── SynonymSetEditor
├── KnowledgeLibraryTable
├── ArticleEditor
├── TaxonomyManager
├── ContentReviewQueue
├── ReindexJobPanel
└── SearchEvaluationResults
```

---

## 25. Loading, empty và error states

- Search telemetry lỗi phải hiển thị Unknown.
- Zero-result empty chỉ có ý nghĩa khi analytics hoạt động.
- Reindex lỗi không được đánh dấu completed.
- Article service lỗi không hiển thị kho tài liệu rỗng giả.
- Không hiển thị raw query nhạy cảm trong lỗi.

---

## 26. Security và privacy

- Backend enforce permission trước search retrieval.
- Search index không chứa secret hoặc field không được phép.
- Snippet và facets phải mask.
- Knowledge article lọc theo permission.
- Reindex và ranking changes phải audit.
- Không gửi raw query hoặc document content vào analytics ngoài policy.

---

## 27. Audit events

```text
search.source.created
search.index.reindexed
search.ranking.updated
search.synonyms.updated
knowledge.article.created
knowledge.article.published
knowledge.article.archived
knowledge.taxonomy.updated
```

---

## 28. Testing

### Unit

- Query normalization.
- Scope filters.
- Synonym expansion.
- Ranking rules.
- Snippet sanitization.

### Integration

- Cross-branch result bị chặn.
- Restricted article không xuất hiện.
- Reindex verification kiểm tra document count.
- Ranking regression chặn publish.

### E2E

- Search SKU chính xác.
- Search tiếng Việt không dấu.
- Filter theo branch.
- Tạo synonym draft.
- Chạy evaluation.
- Publish synonym.
- Reindex và verify.
- Review bài viết stale.

---

## 29. AI-assisted knowledge requirements

Nếu dùng AI:

- Chỉ gợi ý tag, summary, duplicate detection hoặc draft.
- Không tự publish.
- Không gửi restricted article tới provider chưa phê duyệt.
- Không bịa nguồn hoặc liên kết.
- Human review bắt buộc trước publication.

---

## 30. AI coding agent requirements

AI coding agent phải:

- Không lọc permission chỉ ở client.
- Không log raw sensitive queries.
- Không dùng fuzzy matching mạnh cho identifiers.
- Không publish ranking/synonym changes thiếu evaluation.
- Không coi index telemetry lỗi là healthy.
- Bổ sung test cho cross-scope search, typo handling, snippet masking và reindex verification.

---

## 31. Acceptance checklist

- [ ] Có Search Sources và Index Health.
- [ ] Có search analytics và query privacy.
- [ ] Có ranking, synonym và typo tolerance.
- [ ] Có facets theo permission.
- [ ] Có Knowledge Library và lifecycle.
- [ ] Có taxonomy và content-quality checks.
- [ ] Có review queue.
- [ ] Có reindex workflow.
- [ ] Có search evaluation suite.
- [ ] Có security, audit, responsive và accessibility.
- [ ] Có testing và Visual QA đầy đủ.

---

## 32. Những điều không được làm

- Không trả dữ liệu rộng rồi lọc ở client.
- Không log raw query chứa PII hoặc secret.
- Không dùng fuzzy matching mạnh cho mã đơn/SKU.
- Không publish synonym/ranking thiếu test.
- Không để bài viết published thiếu owner hoặc review date.
- Không coi index unknown là healthy.
- Không chạy full reindex thiếu impact review.

---

## 33. Kết luận

Search & Knowledge Management Center của Cynca VLXD phải bảo đảm người dùng tìm đúng dữ liệu và tài liệu trong đúng permission, scope và độ mới. Chất lượng tìm kiếm cần được đo, kiểm thử và quản trị bằng source registry, index health, ranking, taxonomy, content ownership và reindex workflow rõ ràng.

File tiếp theo:

```text
59-Customer-Identity-Profile-Center.md
```