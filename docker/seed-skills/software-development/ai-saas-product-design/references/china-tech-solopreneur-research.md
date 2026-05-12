# China tech → Vietnam solopreneur research pattern

Use when user wants startup ideas from emerging China tech/business models, especially for Vietnamese solopreneurs using AI/no-code/vibe coding.

## User workflow preferences learned

- Do not rely on generic GitHub Trending only; user wants **China tech/mô hình đang bùng → VN chưa có/chưa tốt → dễ kiếm tiền**.
- If user asks for speed, avoid slow `delegate_task`; run direct web/manual research and produce useful output fast.
- Reports should be saved as **HTML files in the codebase** so user can read/archive them, not only chat text.
- Pin cron model when user worries model may drift.
- Prefer ideas that can be sold before build, with buyer, price, MVP, prompt, channel, metric, moat.

## Better search strategy

Avoid broad queries like `China AI 2026`; they often return generic/rubbish results. Search specific sources + terms:

```txt
site:technode.com China AI commerce 2026
site:kr-asia.com China AI ecommerce 2026
site:36kr.com AI 电商 2026
site:36kr.com AI agent 跨境电商 2026
site:36kr.com 数字人 直播 电商 2026
site:36kr.com AI 视频 Kling 可灵 2026
site:36kr.com AI 员工 自动化 2026
site:36kr.com AI Agent 办公 自动化 2026
site:chinainternetwatch.com Xiaohongshu ecommerce 2026
site:walkthechat.com WeChat private domain traffic 2026
site:equalocean.com China AI ecommerce 2026
site:prnewswire.com Alibaba International AI agents merchants
site:alibabagroup.com AI agents global merchants
site:en.people.cn China ASEAN AI cross-border e-commerce 2026
site:scmp.com China digital human livestream commerce AI 2026
site:restofworld.org China AI livestream ecommerce
Kling AI China video generation ecommerce 2026
可灵 AI 视频 电商 短剧 营销 2026
```

## High-signal trend buckets

1. **Cross-border ecommerce AI agents** — Alibaba International, SHEIN, Temu, Shopee/Lazada sellers, AI listing, translation, sourcing, compliance, support.
2. **AI video/Kling/Kuaishou** — product videos, short-drama ads, UGC ads, automated TikTok/Douyin channel factory.
3. **Digital human/live commerce** — 24/7 AI host, live scripts, comment replies, avatar clips.
4. **Xiaohongshu-style search commerce** — review notes, KOC seeding, social SEO, content matrix.
5. **WeChat private-domain traffic** — CRM, mini programs, groups, membership, reactivation.
6. **AI employee / automation skills** — sales ops, support, data entry, procurement, accounting OCR, HR screening, reports, meeting notes, RPA+LLM/browser agents.
7. **Douyin/Meituan local life** — vouchers, booking, local deal campaigns, merchant ops.

## Report file shape

Save reports under:

```txt
D:\code\startup-idea\reports\YYYY-MM-DD-china-tech-vn-solopreneur.html
```

HTML should include:

- title + timestamp
- source links
- China signals section
- VN gap section
- score table: China proof, VN gap, speed-to-revenue, moat, solopreneur fit, total
- cards for top ideas
- idea archive table with slug, tags, status, date, link
- “làm ngay hôm nay” section with 24h steps and DM script

## Manual report script pattern

If cron is flaky, create/run a Python script in the codebase, e.g.:

```powershell
cd D:\code\startup-idea
py manual_china_report.py
```

Use `requests`, `beautifulsoup4`, and `lxml` for search scraping when installed:

```powershell
py -m pip install requests beautifulsoup4 lxml
```

Do not persist “Python missing” style environment failures as rules; prefer `py` on Windows if `python3` hits Microsoft Store alias.

## Example top ideas from prior session

- `AI Cross-border Seller Copilot cho seller Việt` — paste 1688/Taobao link → landed cost, margin, Shopee/TikTok listing, video scripts, FAQ. Strong signals: Alibaba International AI agents, China-ASEAN AI+Cross-border E-commerce.
- `Kling Video Channel Factory` — product info/images → hooks, UGC scripts, Kling prompts, captions, posting calendar; sell done-for-you 30 AI videos/month.
- `AI Nhân viên Ops` — extract/clean customer/order data from chat/CSV → Google Sheet/report/tasks; sell setup + monthly to SMEs.

## Output tone

Vietnamese, short, practical. Avoid blog style. User accepts terse directness and prefers concrete “what to sell, who pays, price, first DM” over abstract analysis.
