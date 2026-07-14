# Bản đồ skill `create-visual-explainer`

Tài liệu này liệt kê **toàn bộ file** trong skill và cho biết mỗi file chứa gì, khi nào load. Đọc cái này trước khi sửa skill — nó là source-of-truth về cấu trúc.

> Skill được chia thành **3 bucket** (3 thư mục) theo bản chất nội dung:
> - **`foundations/`** — nguyên lý xuyên suốt (design tokens + content principles)
> - **`components/`** — UI primitives tái sử dụng, độc lập usecase
> - **`usecases/`** — recipe cho từng loại trang cụ thể

---

## Cấu trúc thư mục

```
create-visual-explainer/
├── SKILL.md                       # Router (luôn được Claude load)
├── MAP.md                         # File này
├── CHANGELOG.md                   # Lịch sử thay đổi
├── CHANGELOG-archive.md           # Lịch sử thay đổi (cũ, archived)
├── assets/
│   └── starter-template.html      # HTML boilerplate sẵn dùng
├── evals/
│   ├── README.md                  # Hướng dẫn chạy eval
│   └── evals.json                 # Test prompts
├── foundations/                   # 7 file — nguyên lý xuyên suốt
│   ├── tokens.md
│   ├── pedagogy.md
│   ├── structure.md
│   ├── verification.md
│   ├── plot-quality.md
│   ├── palettes.md
│   └── backgrounds.md
├── components/                    # 7 file — UI primitives
│   ├── core.md
│   ├── navigation.md
│   ├── numeric.md
│   ├── dashboard-chrome.md
│   ├── asides.md
│   ├── charts.md
│   └── reader-tools.md
└── usecases/                      # 5 file — recipe cho từng loại trang
    ├── math-learning.md
    ├── interactive-explainer.md
    ├── longform-study.md
    ├── progressive-disclosure.md
    └── analytical-report.md
```

---

## Chi tiết từng file

### `SKILL.md` (router, ~280 dòng)
Vai trò: file duy nhất Claude load mặc định khi skill trigger. Chứa:
- §1 Skill map — bảng "load file gì cho task gì" (chia 3 bucket)
- §2A 10 visual immutable rules — nguyên tắc thiết kế bất di bất dịch
- §2B 6 content immutable rules — nguyên tắc nội dung
- §2C Clarification protocol — quy trình hỏi user trước khi generate
- §2D Progressive disclosure — layout đọc mặc định
- §2E Reader tooling — layer hỗ trợ chú ý mặc định (progress, done-tick, focus mode, notes)
- §3 Workflow — flow chuẩn cho 1 educational HTML page
- §4 Quick token cheatsheet — token CSS most-used (cho task trivial inline)
- §5 Anti-patterns — visual + content anti-patterns

---

### `foundations/` — nguyên lý xuyên suốt

#### `foundations/tokens.md` (~589 dòng)
**Vai trò**: design system. Color/typography/spacing/motion tokens cho cả dark mode và light mode, cộng **aesthetic skins** (§5.5: default vs editorial — đổi cả font + surface + palette qua `data-skin`).
**Load khi**: bất kỳ output nào có visual.
**Quick map sections**:
- §1 Color tokens (surfaces, text, accents, chart palettes, grid texture, opacity)
- §2 Typography (Bebas Neue / DM Sans / JetBrains Mono — kèm font sizes, page header pattern)
- §3 Spacing scale (4px base, full scale)
- §4 Radius / motion / breakpoints
- §5 Light mode (opt-in via `[data-theme="light"]`)
- §6 Token usage examples

#### `foundations/pedagogy.md` (~250 dòng)
**Vai trò**: nguyên tắc giải thích — HOW to explain, không phải WHAT to draw.
**Load khi**: bất kỳ educational content nào (baseline).
**Quick map sections**:
- §1 Mục tiêu (reader-first)
- §2 Layered explanation (Layer 1 cartoon → Layer 2 mechanism → Layer 3 formula)
- §3 Concrete before abstract (worked example trước theorem)
- §4 Address misconception (báo trước niềm tin sai phổ biến)
- §5 Cognitive load budget (≤3 thuật ngữ mới/section)
- §6 Layered explanation cho code/math
- §7 Comprehension-first form (chọn dạng theo working memory, không theo aesthetic)
- §8 Visual-first vs prose-first
- §9 Self-test pre-output (19-year-old test)

#### `foundations/structure.md` (~65 dòng)
**Vai trò**: suy ra bố cục macro của trang từ *hình dạng* của kiến thức — đối ứng cấp-trang của `plot-quality.md`; layout lệch hình dạng = *structural lie*.
**Load khi**: bất kỳ educational page nào, **đọc TRƯỚC khi chọn usecases**.
**Quick map sections**:
- §1 Nguyên tắc — một layout có thể nói dối (structural lie, đối ứng trục-y-cắt)
- §2 Gọi tên hình dạng → match layout (bảng shape→layout + cột "lời nói dối nếu lệch"; 8 hình dạng gồm cả cycle)
- §3 Hình dạng lai & lồng nhau (dominant shape làm spine, sub-shape trong section con)
- §4 Self-check im lặng trước khi style (name / match / spine-as-a-sentence test)

#### `foundations/verification.md` (~282 dòng)
**Vai trò**: quy trình verify math/code/citation trước khi output, và format verification trail.
**Load khi**: bất kỳ deliverable nào có math/code/factual claim.
**Quick map sections**:
- §1 Pre-output verify checklist
- §2-§7 Verification methods cho từng loại claim (math, code, dates, sources, ...)
- §8 Verification trail line — format chuẩn để kết thúc deliverable
- §9 Minimum viable verification

#### `foundations/plot-quality.md` (~235 dòng)
**Vai trò**: pre-flight checklist cho mọi plot — chọn loại plot, label đúng, không gian dối số liệu.
**Load khi**: page có chart bất kỳ.
**Quick map sections**:
- §1 Should this be a plot at all? (chọn format đúng)
- §2 Required-annotations checklist (axis labels, units, title, ...)
- §3 Choosing axis ranges
- §4 Plot lie detector (10 patterns "nói dối")
- §5 Render-bug checks (sau khi vẽ)
- §6 Caption template
- §7 Domain-specific gotchas (time series, distributions, ML curves, heatmaps)
- §8 "Would I trust this plot from a stranger?" test
- §9 Chart-type + palette quick reference *(chuyển từ `components/charts.md` §8 vào)*

#### `foundations/palettes.md` (~148 dòng)
**Vai trò**: bộ màu identity theo chủ đề — đổi accent "nhận diện" của trang (eyebrow/title/focus/link…) theo môn, GIỮ NGUYÊN màu semantic (success/danger/chart). Chống "mỗi trang một màu y hệt".
**Load khi**: build trang muốn theme/đa dạng (mặc định: chọn theo chủ đề).
**Quick map sections**:
- §1 Token identity (`--accent-identity`, mặc định = green, định nghĩa ở `tokens.md` §1.3)
- §2 Luật: identity đổi, semantic không đổi (kèm danh sách bề mặt nào là identity)
- §3 Preset theo chủ đề (Toán/Lý/Hoá/Xác suất/Tự nhiên/Mật mã) + CSS `[data-accent]`
- §4 Quy trình chọn (user > suy ra chủ đề > random trong tập đã verify)
- §5 Thêm chủ đề mới
- §6 WCAG cho hue identity (eyebrow là text nhỏ — phải ≥4.5:1 cả 2 theme)

#### `foundations/backgrounds.md` (~144 dòng)
**Vai trò**: biến thể texture cho layer `body::before` (dots, crosshatch, drift, wave, contour, scanline, glow) — chọn bằng `data-bg`, đều tái dùng scaffold lưới ở `tokens.md` §1.6.
**Load khi**: muốn nền khác lưới vuông mặc định (nửa "nền" của theme set).
**Quick map sections**:
- §1 Cách chọn (`data-bg` trên `<html>`; bỏ trống = lưới mặc định; `="none"` = không texture)
- §2 Biến thể tĩnh (dots, crosshatch, contour, scanline)
- §3 Biến thể động — drift, wave (chỉ animate `transform`, có off-switch reduced-motion)
- §4 Biến thể nhuộm màu identity (glow, không lưới — cho dial "polished")
- §5 Guardrail + thứ được "miễn phí" (tắt sau chart + ẩn khi print kế thừa từ tokens.md)
- §6 Thêm biến thể mới

---

### `components/` — UI primitives tái sử dụng

#### `components/core.md` (~673 dòng) ⭐ file lớn nhất
**Vai trò**: UI primitives căn bản — dùng được cho mọi loại trang.
**Load khi**: page có UI elements.
**Quick map sections**:
- §1 Buttons
- §2 Form inputs
- §3 Cards
- §4 Tables
- §5 Badges/pills/code-tags
- §6 Tooltip & Popover
- §7 Modal/Dialog
- §8 Toast/Alert
- §9 Skeleton/Empty/Spinner
- §10 Code blocks (DS/dev tutorials)
- §11 Tabs
- §12 Stat card (KPI)

> 💡 File này 673 dòng — dùng `view_range` với line numbers từ Quick map (dạng `(L11–66)`) để load đúng section thay vì full file.

#### `components/navigation.md` (~580 dòng)
**Vai trò**: navigation chrome — navbar, sidebar, drawer.
**Load khi**: site nhiều trang, app shells.
**Quick map sections**:
- §1 Top navbar (sticky, persistent)
- §2 Mobile drawer
- §3 Sidebar (dashboards)
- §4 Breadcrumb
- §5 Pagination
- §6 Tabs (cross-ref tới components/core.md §11)
- §7 Skip-link (a11y)
- §8 Footer (marketing/docs)

#### `components/numeric.md` (~302 dòng)
**Vai trò**: hiển thị số liệu compact — không phải full chart.
**Load khi**: page có KPI, progress, sparkline.
**Quick map sections**:
- §1 Progress bar (linear)
- §2 Meter (semantic, with thresholds)
- §3 Sparkline (inline mini-chart)
- §4 Count-up animation
- §5 Stat-card grid
- §6 Numeric-display selection guide

#### `components/dashboard-chrome.md` (~260 dòng)
**Vai trò**: chrome riêng cho dashboard — không phải data display.
**Load khi**: dashboard / admin UI.
**Quick map sections**:
- §1 Segmented control (period picker, mode toggle)
- §2 Filter chip
- §3 Banner
- §4 Avatar / Initials marker

#### `components/asides.md` (~449 dòng)
**Vai trò**: nội dung "ngoài lề" main prose — collapse, margin notes, quiz, glossary.
**Load khi**: study note hoặc tutorial cần show-hide / sidenote / footnote / quiz.
**Quick map sections**:
- §1 Accordion / Collapsible ("show solution")
- §2 Sidenote (Tufte margin note)
- §3 Footnotes
- §4 Self-check / Quiz
- §5 Vocab tooltip
- §6 Pedagogy tie-in (mapping pedagogy goals → component)

#### `components/charts.md` (~373 dòng — đã giảm sau khi tách §8)
**Vai trò**: render chart — UI/CSS/animation. KHÔNG chứa logic chọn loại chart.
**Load khi**: page có chart.
**Quick map sections**:
- §1 Chart container
- §2 Axes
- §3 Legend (use pills)
- §4 Tooltip
- §5 Annotations
- §6 No-data and error states
- §7 Library-specific integration (Recharts/Plotly/Chart.js)
- §8 Animation guidance for charts

> 📌 Phần "chart-type + palette quick reference" (cũ là §8) đã chuyển sang `foundations/plot-quality.md` §9 vì là quyết định pre-flight, không phải styling.

#### `components/reader-tools.md` (~578 dòng)
**Vai trò**: layer công cụ *người đọc* điều khiển, nằm trên nội dung — hỗ trợ chức năng điều hành (executive function), ưu tiên người đọc ADHD/PDHD. Không đổi nội dung trang, chỉ đổi cách đọc nó.
**Load khi**: trang học có ≥5 khái niệm (mặc định, xem `SKILL.md` §2E).
**Quick map sections**:
- §1 Vì sao cần — luận điểm hỗ trợ chú ý (bảng "failure mode → tool") + 3 luật của layer
- §2 Hợp đồng `data-concept` — khoá join duy nhất giữa tick / focus scroll-spy / notes / detail panel; khoá ghép `panelId::conceptId` khi trang có 2 tab độ sâu
- §3 Reading-progress bar (top, 3px, identity accent)
- §4 Done-tick + reading time mỗi concept (`~4 phút`, loại KaTeX/`<pre>` khỏi word count)
- §5 Focus dock — bộ đếm `7/23 đã đọc` + **nhảy tới phần chưa đọc tiếp theo**
- §6 Focus mode — làm mờ bằng `opacity` + `saturate`, **không dùng `blur()`** (kèm lý do)
- §7 Notes panel — launcher mép trái, tự gắn note vào concept đang đọc, lọc "Phần này/Tất cả", sửa/xoá 2-click, xuất `.md`/`.json`, nhập kiểu merge
- §8 Quy ước persistence — `try/catch` bắt buộc (localStorage **không có** trong artifact sandbox), namespace + version key, migrate không wipe
- §9 A11y / print / reduced-motion / thứ tự script (chạy sau KaTeX, inject idempotent)
- §10 Anti-patterns

---

### `usecases/` — recipe cho từng loại trang

#### `usecases/math-learning.md` (~329 dòng)
**Vai trò**: skill làm trang math/DS tutorial — KaTeX, theorem/proof boxes, derivations.
**Load khi**: làm bài tutorial toán/DS, lecture note có nhiều LaTeX.
**Quick map sections**:
- §1 KaTeX setup (10× nhanh hơn MathJax)
- §2 Reading-optimized container
- §3 Equation containers
- §4 Theorem / Definition / Proof boxes
- §5 Inline math conventions
- §6 Step-by-step derivations
- §7 Pedagogy reminders for math content

#### `usecases/interactive-explainer.md` (~461 dòng)
**Vai trò**: skill làm trang Distill-style — slider/sandbox, step-through, A/B comparison.
**Load khi**: trang có interactive demo (Distill / 3Blue1Brown / Brilliant style).
**Quick map sections**:
- §1 Pedagogical structure (Hook → Intuition viz → Naming → Formalism)
- §2 Stage + controls layout (core pattern)
- §3 Code-output split panes
- §4 Annotated diagram pattern
- §5 Step-through walkthroughs
- §6 Comparison panels (A/B, before/after)
- §7 "Try it yourself" sandbox
- §8 Reader-attention guidelines

#### `usecases/longform-study.md` (~213 dòng)
**Vai trò**: navigation aids cho trang dài — TOC sticky, reading progress, anchor links.
**Load khi**: page có ≥3 H2 sections.
**Quick map sections**:
- §1 Table of Contents (sticky right rail)
- §2 Reading-progress bar
- §3 Section anchor (#-on-hover)

#### `usecases/progressive-disclosure.md` (~366 dòng)
**Vai trò**: layout đọc cho trang dày kiến thức — overview/map trước, mỗi concept có *brief* (Layer 1–2 + 1 công thức) luôn hiện, chiều sâu (Layer 3, ví dụ số, code) giấu trong *detail panel* trượt ra từ phải; có drag-to-resize.
**Load khi**: trang dày, người đọc cần lướt hết luận điểm trước rồi mới đào sâu từng điểm.
**Quick map sections**:
- §1 Nguyên lý (advance organizer + progressive disclosure)
- §2 Luật phân tầng — brief vs expand (self-sufficiency test + anti-leak rule + don't-gut rule ở block level)
- §3 Page-overview block (advance organizer)
- §4 Brief + detail panel bên phải + drag-to-resize
- §5 Fallback responsive ≤1024px (sheet hoặc accordion) + print/no-JS reveal (bung `.detail-source` inline, ẩn panel khi in)
- §6 Accessibility & motion
- §7 Two-tier reading modes (tab ① Đơn giản / ② Chi tiết) — cùng `data-concept`, tab ① phải tự đủ, disclosure vẫn lồng nhau bên trong tab ②

#### `usecases/analytical-report.md` (~mới)
**Vai trò**: recipe cho **báo cáo phân tích snapshot** (KPI grid, mỗi chart kèm finding, phần nhận xét, footer nguồn) — người đọc *đọc kết quả* về domain họ đã hiểu, KHÔNG học khái niệm mới. Buộc report vào content pillar (rule 14 + rule 16) để nó là readout chứ không phải dashboard rỗng. Hợp nhất với skin `editorial`.
**Load khi**: quarterly/metric report, cohort/A-B result, research findings, postmortem, investor update — snapshot để đọc, người đọc đã biết domain.
**KHÔNG load khi**: trang *dạy* khái niệm (→ explainer), surface *live/real-time* (→ dashboard-chrome dark), 1 con số lẻ (→ trả lời inline).
**Quick map sections**:
- §1 Discriminator — report vs explainer vs live dashboard (learn-a-concept vs read-results vs glance-react) + tie-breakers
- §2 Load when / Do NOT load when
- §3 Content-pillar binding (vì sao không phải dashboard rỗng — rule 14 findings-per-chart, plot integrity, verification trail)
- §4 Report anatomy (header → method line → KPI grid → anomaly banner → sectioned charts+findings → findings block → source footer)
- §5 Editorial pairing + tag caveat (chart đọc từ `--cat-*` không hardcode; tag = ink trên chip tinted, không phải white-on-solid)

---

## Files NOT mentioned in this restructure (giữ nguyên content)

Các file/folder sau **đã tồn tại từ trước** và **không bị đụng nội dung** trong đợt refactor này:

- `assets/starter-template.html` — HTML boilerplate
- `evals/` — test prompts (`evals.json`) + README
- `CHANGELOG.md` — lịch sử thay đổi (file này nên được update **bởi user/maintainer**, không phải bởi script restructure)
- `CHANGELOG-archive.md` — lịch sử cũ
- `SKILL.md §4` Quick token cheatsheet — giữ nguyên
- `SKILL.md §5` Anti-patterns — giữ nguyên
- 16 immutable rules trong SKILL.md — giữ nguyên
- §2C Clarification protocol — giữ nguyên
- §3 Workflow — giữ nguyên (chỉ cập nhật path tham chiếu trong rule footnotes)

---

## Thay đổi so với cấu trúc cũ

| Cũ | Mới |
|---|---|
| `references/tokens.md` | `foundations/tokens.md` |
| `references/pedagogy.md` | `foundations/pedagogy.md` |
| `references/verification.md` | `foundations/verification.md` |
| `references/plot-quality.md` | `foundations/plot-quality.md` *(thêm §9 Chart-type + palette)* |
| `references/components.md` | `components/core.md` |
| `references/patterns-navigation.md` | `components/navigation.md` |
| `references/patterns-numeric.md` | `components/numeric.md` |
| `references/patterns-dashboard-chrome.md` | `components/dashboard-chrome.md` |
| `references/patterns-asides.md` | `components/asides.md` |
| `references/patterns-charts.md` | `components/charts.md` *(bỏ §8, renumber §9→§8)* |
| `references/patterns-math.md` | `usecases/math-learning.md` |
| `references/patterns-explainer.md` | `usecases/interactive-explainer.md` |
| `references/patterns-longform-nav.md` | `usecases/longform-study.md` |
| (chưa có) | `MAP.md` *(file này)* |
| (chưa có) | `components/reader-tools.md` *(v1.13 — notes + focus tools)* |

**Bonus**: mỗi reference file giờ có line ranges trong "Quick map" (dạng `§1 Title (L11–66)`) → Claude có thể `view_range` đúng section thay vì đọc full file.

**Cross-refs**: tất cả tham chiếu chéo trong skill đã được cập nhật sang path mới (vd `tokens.md §1.4` → `foundations/tokens.md §1.4`). CHANGELOG files giữ nguyên (là lịch sử, không sửa).
