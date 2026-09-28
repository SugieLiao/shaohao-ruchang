# 少耗如常宫 · 势宫产业研究所 项目交接说明书（给豆包）

> 交接人：巴蒂（WorkBuddy）｜交接日期：2026-09-28
> 站点入口：**https://invest.liaohao.cc/industry/**
> 内容源（唯一真源）：Obsidian Vault `/Users/sugieliao/Workspace/GitHub/shaohao-ruchang/`
> 本说明书版本：v1（2026-09-28）。后续如有机制变更，改完本文件后要跟笔记一起 commit。

---

## 〇、一句话：这个项目在干什么

宫主自建的**产业研究体系**：把一条产业链拆成一个个**具体价值链控制点（= Node）**，每天扫一遍新闻/研报/公告/投关，把新事实落到对应 Node，最后映射到 A 股标的。
研究回答「买什么」，交易回答「何时买、买多少」。**研究对象是价值链位置，不是行业、不是公司、不是技术大类。**

三条主线：
1. **日更**：每天把新信息分诊、落证据、留痕（原自动化 18:30 跑，**2026-09-28 起已暂停**，现由豆包按第四节流程手动/按需执行）。
2. **建 Node**：遇到新控制点，宫主点头才立项，按 8 步交付清单建。
3. **三处同步**：本地笔记 / GitHub / 网站，一次改完、三处一致。

---

## 一、目录模型（三个地方，别搞混）

| 角色 | 路径 | 说明 |
|---|---|---|
| **内容源**（改笔记在这） | `/Users/sugieliao/Workspace/GitHub/shaohao-ruchang/02 势宫/0201 产业研究所/` | Obsidian 常驻。**只有这个目录下的 md 会进网站** |
| 站点源码（改页面在这） | `/Users/sugieliao/WorkBuddy/industry/` | `index.html`、`build.py`、`assets/`（marked/mermaid） |
| **发布根**（部署快照） | `/Users/sugieliao/WorkBuddy/invest/` | 整目录快照，一个子目录 = 一个站点。**不要在这改脚本** |
| ⛔ 废弃快照 | `/tmp/cfpub` | 会被系统清空；用它部署 = 整站回退 |
| 凭据 | `/Users/sugieliao/WorkBuddy/invest/secrets.env` | `CF_TOKEN` / `CF_ACCOUNT`（CF Pages）；妙想 `MX_APIKEY` 在 skill 环境里 |

**只有 `02 势宫/0201 产业研究所/` 会进网站并可在线编辑**（Functions 白名单写死）。势宫其他目录（如 0202 市场观测站）改了只需本地 + GitHub，不用 deploy。

---

## 二、三处同步（改完笔记必做，这是硬规矩）

```bash
# 一键（推荐）：前置检查 → build → deploy → git commit+push → 三处校验
zsh ~/.workbuddy/skills/shaohao-ruchang-sync/scripts/sync_all.sh "提交信息"

# 只发网页、不动 git（改了站点页面时用）：
zsh ~/.workbuddy/skills/shaohao-ruchang-sync/scripts/sync_all.sh "" --no-git
```

手动版（脚本不可用时）：

```bash
python3 /Users/sugieliao/WorkBuddy/industry/build.py
bash /Users/sugieliao/WorkBuddy/shared-nav/deploy_all.sh
cd /Users/sugieliao/Workspace/GitHub/shaohao-ruchang && \
find .git -name "*.lock" -delete 2>/dev/null; \
git add -A && git commit -m "NODE-XXX 做了什么" && \
env -u HTTP_PROXY -u HTTPS_PROXY -u http_proxy -u https_proxy git push origin main
```

**顺序是先发布、后提交**：避免留下"仓库已提交但线上没更新"的假完成。

同步后必须三处都验到（缺一处不算完）：

```bash
# ① 本地
grep -n "<关键词>" "/Users/sugieliao/Workspace/GitHub/shaohao-ruchang/02 势宫/0201 产业研究所/<文件>.md"
# ② GitHub
cd /Users/sugieliao/Workspace/GitHub/shaohao-ruchang && git log --oneline -1
# ③ 网站（notes.json 里能搜到）
curl -s https://invest.liaohao.cc/industry/notes.json | python3 -c "import json,sys;d=json.load(sys.stdin);print(len(d),'篇');print([n['id'] for n in d if '<关键词>' in n['content']])"
```

> curl 一律加 `env -u HTTP_PROXY -u HTTPS_PROXY -u http_proxy -u https_proxy`（代理会给 GitHub/curl 造成 502）。

---

## 三、核心概念速查（看不懂任何一个就先别动手）

| 概念 | 含义 | 硬规则 |
|---|---|---|
| **Node** | 最小研究单位 = 一个**价值链控制点**（例：玻璃基板的中游"TGV 全制程加工"）。不是行业、不是公司、不是技术大类 | 建 Node 有 8 步交付清单（见第六节） |
| **十三步** | Node 报告的固定骨架：Supply Chain → BOM → Value Chain → Bottleneck/Scarce Layer → Profit Pool → Profit Migration …（完整版见 `产业研究框架.md` 与各 Node 报告） | Step 12 必须写出**可证伪的失败条件** |
| **EVD-XXX** | 证据编号，**跨 Node 全局连续**。取号用脚本，不手编 | 每条实质更新必须有 EVD |
| **证据等级** | A=公司公告/投关/互动易；B=厂商官方稿/券商研报；C=媒体；D=聚合帖（无机构署名） | D 级**永远**不足以触发结论句改动 |
| **REL-XXX** | Node 之间的关系（替代 / 上下游 / 技术同源·价值链分离），登记在 `研究路由表.md` | 新建 Node 必须同步登记 |
| **KA-XXX** | 知识资产编号。已入宫：KA-013 框架暗线六条、KA-014 研究路由表、KA-015 行业地图更新记录、KA-016 重估与并列存疑清单、KA-017 配图登记册 | — |
| **更新日志** | 每个 Node 末尾 `## 更新日志（Changelog）`，一天一行 | 用 `add_entry.py` 写，**别手写表格** |
| **总账** | `行业地图更新记录.md` 第二节，一天一行 | 同上 |

**Node 检验法（立项前自测）**：Supply Chain 七问的答案**不能分裂**（若多项答案指向不同主体/环节，说明它其实是两个 Node）；且 Step 12 必须能用**一个**变化证伪整个 Node。
**切分判据（2026-09-27 升级）**：同一技术对 A 股的**方向相反**时，一律按方向切分，优先于"能否分别证伪"（NODE-009 电链受益 / NODE-010 光链受损，就是这么拆的）。

---

## 四、每日扫描 SOP（Step 0–6）

```bash
PY=/Users/sugieliao/.workbuddy/binaries/python/versions/3.13.12/bin/python3
S=~/.workbuddy/skills/shaohao-daily-node-scan/scripts

# Step 0  生成扫描清单 + 取「下一可用 EVD 编号」（每天从这里开始，不许凭记忆）
$PY $S/node_index.py --md

# Step 1  采集（妙想为主，配额耗尽降级 tdx / mx-ds-mcp / WebSearch）
$PY $S/fetch_news.py --days 3 -o ~/WorkBuddy/Temp/scan_$(date +%Y%m%d).md

# Step 2  分诊（严格按研究路由表第三节）
#   命中单个 Node → 直接更新 ｜ 命中多个 / 部分命中（含新实体）→ 暂停，提交分诊报告给宫主
#   完全未命中 → 记为候选种子，报宫主是否立项（禁止擅自建 Node）

# Step 3  更新：追加证据（编号不手写）
$PY $S/add_evidence.py --node NODE-003 --evd EVD-220 --level B \
  --source "来源" --date 2026-09-28 --summary "一句话事实"

# Step 4  写记录（先 --dry 看行对不对，再真写）
$PY $S/add_entry.py node NODE-003 --date 2026-09-28 --type 研报 \
  --trigger "触发信息" --where "Step 6 稀缺层" --impact 强化 --evd EVD-220
$PY $S/add_entry.py map   --date 2026-09-28 --scanned 10/10 --hits N --updates N \
  --evd "EVD-220~EVD-22X" --pending N --sync "本地✓/线上✓/GitHub✓" --note "当日一句话"
$PY $S/add_entry.py cross  --date ... --trigger ... --main NODE-005 --second "NODE-004" --path ... --action ...
$PY $S/add_entry.py pending --date ... --topic ... --kind 新实体 --nodes NODE-007 --status "..."

# Step 5  三处同步
zsh ~/.workbuddy/skills/shaohao-ruchang-sync/scripts/sync_all.sh "行研日更 2026-09-28：…"

# Step 6  向宫主汇报四段：今天改了什么 / 新增证据编号区间 / 需要你拍板的 / 没动但值得盯的
```

**采集纪律**：每个 Node 3–8 条候选，宁缺毋滥；同一事件多篇报道算**一条**（同源升级原 EVD，不新建）。

⚠️ **妙想配额假象（连续五轮踩到）**：`news-search` 配额耗尽返回 `code 113`，但 `fetch_news.py` **不报错、直接输出 0 条**。
**判定纪律：出现「0 条候选」一律不准直接记「无实质更新」，必须降级 WebSearch 复核。**（10 Node × 2 关键词 = 20 次调用，一轮就打满 10 次/天配额。）

---

## 五、改 Node 的三类动作 + 改写护栏

| 情形 | 动作 |
|---|---|
| **确认 / 强化** | 可直接改段落的数值、时间、进度 + 追加 EVD |
| **削弱** | 更新段落 + 标「存疑」；若存在互斥另一说 → 走矛盾并列规则 |
| **证伪**（命中 Step 12 失败条件） | **可改写结论句**（宫主 2026-09-27 允许），但须满足护栏第 4 条 |
| **待观察 / 无实质影响** | 只在更新日志留痕，正文不动 |

**改写护栏四条（缺一不可）**：
1. **留痕原句 → 新句**：更新日志「变更位置」列写明**原结论句原文与新结论句原文**，不能只写"已更新"。
2. **总账同步**：`行业地图更新记录.md` 点明改了哪个 Node 的哪一句。
3. **并列项仍不动**：已在《重估与并列存疑清单》登记的议题，**结论句继续一字不改**。
4. **等级门槛**：确认/强化 C 级即可；**削弱/证伪须 A 或 B 级**；D 级永不触发。

**克制的原則**：能靠"加一条证据 + 一行日志"表达的，就不要改结论句——结论句是骨架，防止逐日漂移。

---

## 六、新建 Node 的 8 步交付清单（宫主点名 = 已批准，可直接落 main）

| 步 | 动作 | 漏了会怎样 |
|---|---|---|
| 1 | 定**价值链位置**，不得升格为技术大类 | Node 越做越宽，Step 12 写不出可证伪条件 |
| 2 | 建文件 `NODE-XXX 中文名 English Name.md` | 文件名不合规 → 站点分组异常 |
| 3 | 骨架齐四件：十三步正文 / 第五节《证据索引》/ 第六节《研究反馈》/ 末尾 `## 更新日志（Changelog）` | 没有更新日志 → 日更无处落行 |
| 4 | **路由表改四处**（最易漏）：Node 索引加行 / 关系索引加 REL-XXX / 证据索引标题与 EVD 区间同步 / 实战反馈加行 | 路由表查不到 → 下次分诊会当新实体重复立项 |
| 5 | 《行业地图更新记录》第二节总账加一行 | 地图变化无账可查 |
| 6 | 矛盾项进《重估与并列存疑清单》C-XX | 结论里混进未裁决判断 |
| 7 | `sync_all.sh` 三处同步 | 本地有、线上没有 |
| 8 | 校验 `notes.json` **篇数 +1** 且新 Node 在其中 | 发布失败被误判成功 |

> 分支规矩：宫主点名/直接指令 = 已批准，落 `main`；**自己发起的**建设（新 Node 立项、框架大改）走 `node/xxx` 分支 + Draft PR 等宫主 Merge。

---

## 七、矛盾并列规则（**优先于一切裁决动作**）

> 宫主 2026-09-23 指令：「有疑问的、矛盾的信息，两者都列出来，不用你出判断，我也暂时不用判断。」

触发条件（任一）：① 两信源互斥结论；② 同一事实口径冲突（数值量级/命名/时间）；③ 方向相反；④ 公司与市场定性相反。

**动作只有四步**：
1. 《重估与并列存疑清单》新增 `C-XX`：议题 / 涉及 Node / 冲突性质 / **说法 A（来源·日期·等级·EVD）** / **说法 B（同）** / 状态「并列保留，不裁决」。
2. 两侧**各挂一条 EVD**（不要合成一条"有争议"）。
3. 对应 Node 更新日志加一行 `类型=重估`、`影响判定=并列存疑`，回指 C-XX。
4. **正文结论句一个字不改。**

**禁止**：写"我倾向…""应以…为准"；给宫主出 A/B 选择题；把并列项塞回待裁队列催；**做换算/取中值/猜口径**（即使你怀疑差异来自"累计 vs 单项"，外部没区分就一律并列）。
**消解时新增一行**（注明日期与新证据），**不修改历史行**。

---

## 八、当前家底（2026-09-28 20:47 实测）

**下一可用证据编号：EVD-220**

| Node | 名称 | 证据数 | 备注 |
|---|---|---|---|
| NODE-001 | HBM 高带宽内存 | 26 | 首个 Node，框架示例链 |
| NODE-002 | 前驱体 Precursor | 19 | 上游材料 |
| NODE-003 | 华为超节点 Huawei SuperNode | 32 | 系统级产品；有阿里云磐久归属待裁 |
| NODE-004 | OCS 光电路交换机 | 30 | 器件级 |
| NODE-005 | oDSP | 27 | 与 008 构成对偶（价值链 vs 技术大类） |
| NODE-006 | AI芯片 GPU/CPU/TPU/ASIC | 19 | 计算层 |
| NODE-007 | InP 磷化铟 | 25 | 中国握反向筹码 |
| NODE-008 | DSP 数字信号处理器 | 19 | 因"关系澄清"新增 |
| NODE-009 | 玻璃基板（**电链**） | 23 | 期权型，2026 行业零量产收入 |
| NODE-010 | 玻璃基光互连 Glass Bridge（**光链**） | 15 | **本宫第一个 A 股主体为受损方的 Node** |

- 配图：10 Node × 3 张 = **30 张**（`assets/`，线上全部 200）
- 站点：16 篇（10 Node + 研究路由表 / 产业研究框架 / 框架暗线六条 / 行业地图更新记录 / 配图登记册 / 重估与并列存疑清单）
- **池分类（Alpha 池 / 观察池 / 风险池）目前是提案状态**，判据已向宫主解释，**等宫主裁定归档**，不要擅自给 Node 打池标签。

### 待宫主裁决（挑主要的，完整表见《行业地图更新记录》第四节）

1. NODE-009 / NODE-010 归入观察池还是风险池（宫主 2026-09-27 已提问未拍）
2. 阿里云磐久 AL64/AL144 是否纳入 NODE-003 或另立「云厂商超节点」（已落 EVD-207/208）
3. 水晶光电（002273）是否登记为 NODE-010 受益侧映射（EVD-219，仅第三方转引）
4. 大庆溢泰 / 南京集溢（InP 衬底一级市场）是否入 NODE-007（EVD-214）
5. 芯信光科技（MEMS-OCS 整机）是否入 NODE-004（EVD-160）
6. 华勤技术是否入 NODE-003 映射（EVD-147）
7. 彩虹股份 TGV 送样良率 80% 是否调整 NODE-009 原片段定性（EVD-196）
8. 捷佳伟创 / 海目星 / 中国宏光 是否入 NODE-009（EVD-196）

### 遗留缺口（可做但不必急）
- NODE-010 缺 Glass Bridge 实物照（现为康宁官方渲染图，已打角标）
- NODE-009 缺 IOX 波导剖面图
- NODE-005 IMG-018 来源页未锁定；NODE-007 缺单晶炉实拍

---

## 九、配图规范（改图前必读）

- 存放 `02 势宫/0201 产业研究所/assets/`；命名 `node-XXX-<slug>.jpg`（**全小写 ASCII**，中文/空格路径在 CF Pages 上会 308）
- 引用 `![](assets/xxx.jpg)`（**禁用** Obsidian `![[]]` wiki 语法，GitHub 与网页都不渲染）
- 规格：JPEG 长边 ≤1600px（**小图不放大**）、单张 ≤300 KB；每 Node 3–6 张
- 图注：图下方斜体一行 = 「这张图说明什么 + 来源 + 日期 + 署名」

**角标规则（宫主 2026-09-28 放宽后）**：拿不到实拍时，允许用 ① 官方渲染图 ② AI 生成图 ③ 带水印转引图 ④ 示意图，但**必须在右上角打角标**：

```bash
/Users/sugieliao/.workbuddy/binaries/python/envs/default/bin/python \
  ~/.workbuddy/skills/shaohao-node-images/scripts/tag_image.py <图> render|ai|watermark|diagram
```

**不变式：有角标 = 非实拍；无角标 = 实拍。** 该打不打会破坏整条规则。打标前**先备份原图**（原地覆盖、重复打会叠加）。
底线：**来源可核**（禁聚合帖盗图/来源不明）；带水印图**保留原水印**；AI 图不得冒充具体型号实物。
采集三板斧：厂商官方 PDF 抽图 → 网页抽图脚本 → `wsrv.nl` 图片代理（Wikimedia/防盗链唯一通道）。**每张必须目视确认**（门户首图常是广告位，曾抓到火山喷发图）。

---

## 十、站点维护（只在你改页面时需要）

- 源码：`/Users/sugieliao/WorkBuddy/industry/index.html`（单页 SPA + marked + mermaid）、`build.py`（扫 md → notes.json + 拷资源）
- 前端已修的两个坑（**别回退**）：
  1. **mermaid 节点第二行被裁**：`<style>` 里有 `.nodeLabel,.edgeLabel,.label,.cluster-label{font-size:16px;line-height:1.4}` + `.mermaid foreignObject{overflow:visible}`。规则**必须全局**（mermaid 在 body 临时容器量测，只写 `#doc` 下等于没改）。
  2. **表格股票名称折行**：`#doc th.nw,#doc td.nw{white-space:nowrap}` + `render()` 里调 `nowrapShortCells(d)`（≤18 字且不含 `，。；` 的单元格加 `.nw`）。
- 改完自检（两个脚本，无头浏览器实测）：

```bash
python3 ~/.workbuddy/skills/liaohao-shared-nav/scripts/mermaid_clip_check.py   # 应 CLIPPED=0
python3 ~/.workbuddy/skills/liaohao-shared-nav/scripts/table_wrap_check.py     # 应 OLD_BAD>0 且 NEW_BAD=0
```

- 表格新增行要按**显示宽度**对齐（中文算 2 列宽），用 `unicodedata.east_asian_width` 计算填充，别手敲空格。

---

## 十一、故障速查（出事先查这张表）

| 现象 | 根因 | 处理 |
|---|---|---|
| `curl http=000` + TLS `internal error` | 域名 DNS 没指向 Pages（`dig CNAME` 返回 `sp.gname.net`） | 去注册商改 CNAME。**先 curl `invest-49u.pages.dev` 排除内容问题** |
| `http=522` | CF 认域名但源站无响应 | 查 Pages 部署状态 |
| `403 + error code 1014` | 跨账户绑定被禁 | 换域名或改绑 |
| 发布"卡住"很久 | **wrangler 打印 `✨ Deployment complete!` 后不退出**（挂 40 分钟也正常） | 看 stdout 有没有 `Deployment complete!`；有就直接 curl 线上核对，然后 kill 残留进程 |
| `Brokered host copy overwrite refused` | 沙箱拒绝覆盖已存在文件（`shutil.copy` 必踩） | build.py 里所有复制走 `copy_if_changed()`（内部用 `open(dst,'wb')` 逐块写） |
| `Pages only supports files up to 25 MiB` | 发布根有超限文件，**会让整站发布失败** | `deploy_all.sh` 已自动移到 `WorkBuddy/Temp/oversized_pub_backup/` |
| git push 502 / 多层 lock | 代理 + Obsidian 持续重建锁 | `env -u HTTP(S)_PROXY` push；`find .git -name "*.lock" -delete` 与 git 命令写**同一条** |
| 线上还是旧内容 | CF 边缘缓存，**`?v=` 绕不过** | 以 curl 到的 `notes.json` 为准，等几分钟 |
| 域名不对 | ⛔ `invest.liao.cc`（少 hao）不存在 | 正确域名只有 **`invest.liaohao.cc`**；原站 `invest-49u.pages.dev`；Pages 项目名 **`invest`** |

---

## 十二、你的执行边界（先看这一节再动手）

- **如果你有本机执行能力**（能跑 shell / 读写上述路径）：按本文命令直接干，改完走三处同步 + 验收。
- **如果你只有对话能力**（豆包 App / 网页版，**没有本机文件与终端**）：不要假装执行。做法是——
  1. 让宫主贴给你需要的文件片段；
  2. 你产出**「待落地清单」**：`文件路径 + 改动位置（原句→新句） + 新增 EVD 行 + 更新日志行 + 待执行的一条同步命令`；
  3. 由宫主或巴蒂在本机落地并回传结果。
  这样分工是允许的，也是最快的。

---

## 十三、第一批可做的工作（按优先级）

1. **验收环境**（10 分钟，先做这个）：
   - `node_index.py --md` → 确认 10 Node / EVD-220
   - 两个前端自检脚本 → 确认 `CLIPPED=0`、`NEW_BAD=0`
   - 三端一致性：`notes.json` 16 篇 + `git log --oneline -1`
2. **从待裁队列挑一项做深挖**（第 3 项「水晶光电是否入 NODE-010 映射」材料最齐，只差公司公告/互动易确认）——**找出公司官方口径就升级为 A 级，找不到就如实写"未获确认"，不要脑补**。
3. **补齐遗留配图**：NODE-010 Glass Bridge 实物照、NODE-009 IOX 波导剖面图、NODE-007 单晶炉实拍（按第九节规范 + 角标）。
4. **不要做**：擅自立项新 Node、裁决任何 C-XX、给 Node 打池标签、改结论句（除非满足四条护栏）。

---

## 十四、验收清单（干完怎么证明你没搞错）

- [ ] 改的每一处都有 EVD 编号，且编号连续、不重复（同源事实是**升级**原 EVD，不是新建）
- [ ] 每个被改的 Node，更新日志**有一行**（`add_entry.py` 写的，非手敲）
- [ ] `行业地图更新记录.md` 总账**有一行**，含 scanned/hits/updates/EVD 区间/sync
- [ ] 结论句如被改写：更新日志里有**原句→新句**原文、总账点名、等级达 A/B（削弱/证伪类）
- [ ] 矛盾项：进了 C-XX、两侧各挂 EVD、**正文结论句未动**
- [ ] 三处同步完成且三处都验到（本地 grep / git log / notes.json）
- [ ] `notes.json` 篇数正确（新建 Node 时 **+1**）
- [ ] 没在发布根留下 `_tmp*` 之类的临时文件（会被一起发布上线！放 `/Users/sugieliao/WorkBuddy/Temp`）

---

## 十五、红线（违反会直接破坏体系）

1. **不擅自立项新 Node**（宫主点名才算批准）；不擅自把新实体并入 A 股映射表。
2. **不裁决矛盾**——并列是动作，判断不是。
3. **不只改一处**——三处同步是硬规矩。
4. **不手写表格、不手编 EVD 号**——都用脚本。
5. **不改历史记录行**——更正用新行追加。
6. **不用 `/tmp/cfpub` 发布**。
7. 跨项目红线（与本项目相邻但独立）：舍一/地金刚知识库**只做本地分析，绝不联网抓取源内容**（封号与著作权风险）；临时文件一律落 `/Users/sugieliao/WorkBuddy/Temp`。

---

## 附录：相关 skill（本机已有，可直接调用）

| skill | 管什么 |
|---|---|
| `shaohao-daily-node-scan` | 每日扫描 SOP、分诊规则、改写护栏、实战备注（**最有价值的经验在它的"实战备注"六之二～六之七**） |
| `shaohao-ruchang-sync` | 三处同步、新建 Node 8 步清单、CF 域名操作、坑列表 12 条 |
| `shaohao-node-images` | 配图采集、角标、登记册规范 |
| `liaohao-shared-nav` | 行研站页面与菜单维护（改 index.html 时读它） |
| `serenity-skill` | Node 研究方法论与数据源映射 |

**不在本说明书范围**：A股每日复盘站群、盘面全景、宏观看板、投资学习站（见 `/Users/sugieliao/WorkBuddy/A股每日复盘/PROJECT_HANDOFF_豆包.md`）；舍一/地金刚知识库；小七听写 App。

---

## 附录二：已停用的日更自动化（配置原文，如需恢复照此重建）

**状态**：2026-09-28 经宫主确认**已删除**（此前 9-28 先置为 PAUSED，随后彻底删除）。日更改由豆包按第四节流程手动/按需执行，**不要自行新建同名自动化**。

| 字段 | 值 |
|---|---|
| 名称 | 势宫每日行研扫描（Node 地图日更） |
| id | `16f42ca1-fcf1-4e21-a19e-77366e14d7e3` |
| scheduleType | recurring，rrule `FREQ=DAILY;BYHOUR=18;BYMINUTE=30` |
| cwds | `/Users/sugieliao/WorkBuddy/2026-08-17-19-23-24` |

恢复用的 prompt 原文：

> 按 skill `shaohao-daily-node-scan` 跑今日的势宫产业研究所行研扫描更新。
>
> 完整步骤（skill 内 `~/.workbuddy/skills/shaohao-daily-node-scan/SKILL.md` 有细节）：
> 1. 用 `node_index.py --md` 生成 Node 的扫描清单，取下一可用 EVD 编号。
> 2. 按每个 Node 的关键词检索最近 1–3 天的新闻/研报/公告/数据/产品/技术信息。数据源按序降级：妙想 API news-search（curl，环境变量 MX_APIKEY）→ tdx-connector 的 wenda_news_query/wenda_report_query/wenda_notice_query → mx-ds-mcp 的 mx_finance_search_news/mx_finance_search_notice → WebSearch 兜底。每个 Node 3–8 条候选，同一事件的多篇报道算一条。
> 3. 严格按《研究路由表》分诊协议判定归属：命中单 Node 才可直接更新；多 Node 命中、部分命中、或出现新实体，一律暂停并写进《行业地图更新记录》第四节「待宫主裁决队列」，不擅自新建 Node、不擅自改结论。
> 4. 只做「确认/强化」类更新（改数值、时间、进度 + 证据索引追加 EVD）；削弱需加存疑标记并进裁决队列；证伪（命中 Step 12 失败条件）绝不自动改结论。命中已有事实时升级原证据来源，不新建条目。
> 5. 用 `add_entry.py`（先 --dry 再真写）给每个被更新 Node 的《更新日志（Changelog）》加一行，并给《行业地图更新记录.md》第二节总账加一行。一条信息影响多个 Node 时加 cross 横向传导行。
> 6. 全部改完后执行 `zsh ~/.workbuddy/skills/shaohao-ruchang-sync/scripts/sync_all.sh "行研日更 YYYY-MM-DD：<一句摘要>"`，把本地 Obsidian 笔记、liaohao.cc/industry 线上页面、GitHub main 三处同步一致。
> 7. 最后向宫主汇报四段简报：今天改了什么 / 新增证据编号 / 需要拍板的裁决项 / 没动但值得盯的待观察项。
>
> Vault 路径：`/Users/sugieliao/Workspace/GitHub/shaohao-ruchang/02 势宫/0201 产业研究所/`。若当天无实质新信息，也要在地图总账登记一行「无实质更新」，不要静默跳过。

（注：原文步骤 1 写的是「8 个 Node」，实际当前为 10 个 Node，恢复时以 `node_index.py --md` 的实际输出为准。）
