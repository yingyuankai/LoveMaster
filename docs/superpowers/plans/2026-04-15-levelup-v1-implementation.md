# LevelUp v1.0 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Transform the existing LoveMaster project into LevelUp — a goal-driven personal growth system for men, delivered as a Claude Code plugin.

**Architecture:** A Claude Code plugin with 6 skills (1 onboarding + 5 content), 1 agent (weekly check-in), and 1 command (/levelup). The onboarding skill acts as the core engine: it diagnoses the user, writes a personal profile to memory, and generates a growth path. Content skills are triggered automatically based on the user's questions and current stage. All content is in Chinese (Simplified).

**Tech Stack:** Claude Code plugin system (Markdown-based skills, agents, commands). No code dependencies — pure content.

---

## File Structure

```
LevelUp/
├── .claude-plugin/
│   └── plugin.json                          # UPDATE: rename to levelup
├── skills/
│   ├── onboarding/                          # CREATE: diagnosis + path planning
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── diagnosis-framework.md
│   │       └── path-templates.md
│   ├── love-master/                         # EXISTS: keep, minor updates to description
│   │   ├── SKILL.md
│   │   └── references/ (4 files, unchanged)
│   ├── alpacino-charm/                      # EXISTS: keep, minor updates to description
│   │   ├── SKILL.md
│   │   └── references/ (2 files, unchanged)
│   ├── image-craft/                         # CREATE: image management
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── style-guide.md
│   │       └── grooming.md
│   ├── inner-game/                          # CREATE: emotional resilience
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── anxiety-toolkit.md
│   │       ├── confidence-building.md
│   │       └── attachment-theory.md
│   └── social-voice/                        # CREATE: communication skills
│       ├── SKILL.md
│       └── references/
│           ├── conversation-skills.md
│           ├── storytelling.md
│           └── workplace-communication.md
├── agents/
│   └── weekly-checkin.md                    # CREATE: weekly check-in agent
├── commands/
│   └── levelup.md                           # CREATE: /levelup entry command
├── docs/
│   └── README.md                            # CREATE: project readme
├── love-master/                             # DELETE: old duplicate directory
├── 四周行动计划.md                            # DELETE: personal file, not part of plugin
├── 四周行动计划-详细版.md                      # DELETE: personal file, not part of plugin
└── 恋爱指南-完整版.md                         # DELETE: personal file, not part of plugin
```

Note: The 3 personal Chinese-language files (行动计划, 恋爱指南) were created for the original user. They should be moved out of the plugin directory since the plugin should be user-agnostic. We'll move them to a `personal/` directory that's gitignored.

---

### Task 1: Project Restructure

**Files:**
- Modify: `.claude-plugin/plugin.json`
- Modify: `.gitignore`
- Create: `personal/` directory
- Delete: `love-master/` (duplicate of `skills/love-master/`)

- [ ] **Step 1: Update plugin.json**

Replace the contents of `.claude-plugin/plugin.json` with:

```json
{
  "name": "levelup",
  "version": "1.0.0",
  "description": "LevelUp — 男生版自我提升操作系统。目标驱动的 AI 个人成长教练，覆盖恋爱社交、男性魅力、形象管理、情绪管理、沟通表达。",
  "author": {
    "name": "yingyuankai"
  },
  "keywords": ["personal-growth", "self-improvement", "dating", "confidence", "communication"]
}
```

- [ ] **Step 2: Move personal files to personal/ directory**

```bash
mkdir -p personal
mv 四周行动计划.md personal/
mv 四周行动计划-详细版.md personal/
mv 恋爱指南-完整版.md personal/
```

- [ ] **Step 3: Update .gitignore**

Add to `.gitignore`:

```
love-master-workspace/
personal/
```

- [ ] **Step 4: Remove duplicate love-master directory**

The `love-master/` directory at root level is a duplicate of `skills/love-master/`. Remove it:

```bash
rm -rf love-master/
```

- [ ] **Step 5: Create agents/ and commands/ directories**

```bash
mkdir -p agents commands
```

- [ ] **Step 6: Verify plugin structure**

```bash
find . -type f -not -path '*/.git/*' -not -path '*/love-master-workspace/*' -not -path '*/personal/*' | sort
```

Expected: clean plugin structure with `.claude-plugin/`, `skills/`, `agents/`, `commands/`, `docs/`.

- [ ] **Step 7: Commit**

```bash
git add -A
git commit -m "refactor: restructure project as LevelUp plugin

- Rename plugin from love-master to levelup
- Move personal files to personal/ (gitignored)
- Remove duplicate love-master/ directory
- Create agents/ and commands/ directories"
```

---

### Task 2: Onboarding Skill — Core Diagnosis Engine

**Files:**
- Create: `skills/onboarding/SKILL.md`
- Create: `skills/onboarding/references/diagnosis-framework.md`
- Create: `skills/onboarding/references/path-templates.md`

- [ ] **Step 1: Create onboarding SKILL.md**

Create `skills/onboarding/SKILL.md` with the following content:

The SKILL.md should include:
- **Frontmatter**: name `onboarding`, description that triggers on first use, `/levelup start`, `/levelup reset`, or when user mentions wanting to set goals/get started/figure out what to improve
- **Core flow**: Welcome → Goal selection → Basic info → Situational assessment → Preferences → Generate profile + path
- **Goal options**: 找到女朋友 / 变得更自信 / 提升社交能力 / 职场突破 / 提升个人品味 / 全面提升自己 / 自定义
- **Question-by-question guidance**: Each question asked one at a time, with multiple choice where possible
- **Output format**: Personal profile (written to memory) + Growth path document (written to project directory) + First week action plan
- **Tone**: Like a friend helping you figure out what you need, not a clinical assessment
- **Age-adaptive**: Adjust question phrasing and path recommendations based on user's age group (college, early career, mid-career, 40+)

Full content should be approximately 150-200 lines covering the complete diagnosis flow, output format specification, and instructions for writing to memory.

- [ ] **Step 2: Create diagnosis-framework.md**

Create `skills/onboarding/references/diagnosis-framework.md` with:

- Complete question set for each goal type (5-8 questions per goal)
- Scoring/assessment logic for identifying user's strengths and weaknesses
- Age-group specific question variants
- Personality assessment dimensions (introvert/extrovert, risk tolerance, learning style)
- How to identify the user's top 2-3 priority areas

- [ ] **Step 3: Create path-templates.md**

Create `skills/onboarding/references/path-templates.md` with:

- Goal → Module path mapping (from spec section 3.2):
  - 找到女朋友 → 形象管理 → 恋爱社交 → 男性魅力 → 情绪管理
  - 变得更自信 → 情绪管理 → 男性魅力 → 沟通表达 → 形象管理
  - 提升社交能力 → 沟通表达 → 男性魅力 → 恋爱社交 → 形象管理
  - 职场突破 → 沟通表达 → 男性魅力 → 情绪管理 → 形象管理
  - 全面提升 → diagnosis-based ordering of all modules
- Template for 12-week growth path with 4 phases
- Template for weekly action plan
- Age-adjusted path variations (college students get different timelines than working professionals)
- Memory file templates (user-profile.md, user-goal.md, user-progress.md, user-preferences.md)

- [ ] **Step 4: Commit**

```bash
git add skills/onboarding/
git commit -m "feat: add onboarding skill — diagnosis engine and path planning

- Goal selection with 7 preset options + custom
- Age-adaptive diagnosis framework
- Personal profile generation (memory integration)
- Growth path templates for all goal types
- First-week action plan generation"
```

---

### Task 3: Image-Craft Skill — Image Management

**Files:**
- Create: `skills/image-craft/SKILL.md`
- Create: `skills/image-craft/references/style-guide.md`
- Create: `skills/image-craft/references/grooming.md`

- [ ] **Step 1: Create image-craft SKILL.md**

Create `skills/image-craft/SKILL.md` with:

- **Frontmatter**: name `image-craft`, description triggers on 穿搭/发型/护肤/形象/穿什么/怎么搭配/体态/打扮/造型/约会穿什么/面试穿什么
- **Core capabilities**:
  1. 形象诊断：通过对话了解用户当前的穿衣风格、体型、预算，给出改善方向
  2. 穿搭指南：按场景（日常/约会/职场/运动）推荐搭配方案
  3. 发型建议：按脸型和气质推荐，含如何跟理发师沟通
  4. 体态改善：站姿/坐姿/走路姿态的具体纠正方法
  5. 护肤入门：基础流程和产品推荐（按预算分档）
  6. 朋友圈形象：拍照姿势、角度、什么值得发
- **Response format**: 先问清场景和预算，再给具体方案。附图片描述让用户能搜索参考
- **Principles**: 干净 > 贵，合身 > 潮流，适合自己 > 照搬别人

- [ ] **Step 2: Create style-guide.md**

Create `skills/image-craft/references/style-guide.md` with:

- 男装基础单品清单（必备 vs 加分）
- 体型 × 风格搭配矩阵（偏瘦/标准/偏胖 × 日常/商务/约会）
- 色彩搭配基础规则（安全色组合、进阶搭配）
- 按预算分档的购物指南（500以内/500-2000/2000+）
- 季节搭配要点（春夏/秋冬）
- 配饰指南（手表、包、鞋的选择）
- 常见雷区（什么不要穿）

- [ ] **Step 3: Create grooming.md**

Create `skills/image-craft/references/grooming.md` with:

- 脸型 × 发型推荐矩阵
- 怎么跟理发师沟通（话术模板）
- 基础护肤流程（洁面→水→乳→防晒）
- 按预算的产品推荐
- 体态自查清单和纠正动作
- 手部/牙齿/体味等细节管理
- 拍照技巧（角度、光线、构图）

- [ ] **Step 4: Commit**

```bash
git add skills/image-craft/
git commit -m "feat: add image-craft skill — men's image management

- Style guide with body-type matrix and budget tiers
- Grooming guide covering hair, skincare, posture
- Scene-based outfit recommendations
- Photo and social media image tips"
```

---

### Task 4: Inner-Game Skill — Emotional Resilience

**Files:**
- Create: `skills/inner-game/SKILL.md`
- Create: `skills/inner-game/references/anxiety-toolkit.md`
- Create: `skills/inner-game/references/confidence-building.md`
- Create: `skills/inner-game/references/attachment-theory.md`

- [ ] **Step 1: Create inner-game SKILL.md**

Create `skills/inner-game/SKILL.md` with:

- **Frontmatter**: name `inner-game`, description triggers on 焦虑/紧张/不自信/内耗/患得患失/自我怀疑/害怕被拒绝/安全感/情绪/压力大/睡不着/胡思乱想/自卑/社恐
- **Core capabilities**:
  1. 焦虑急救：当用户正在焦虑时，给出立刻可用的缓解方法
  2. 自信心建设：从认知层面重建自信，具体可执行的练习
  3. 依附模式分析：帮用户理解自己在关系中的行为模式
  4. 内耗管理：过度思虑/反刍/自我怀疑的打断技巧
  5. 拒绝恢复：被拒绝后的情绪处理和心态重建
  6. 社交焦虑：在社交场合紧张的具体应对
- **Response format**: 先共情（"我理解这种感觉"），再给认知重构（"换个角度看"），最后给行动方案（"现在你可以做这一件事"）
- **Principles**: 不说"想开点"这种废话。每个建议都要有心理学依据，但用大白话解释

- [ ] **Step 2: Create anxiety-toolkit.md**

Comprehensive anxiety management reference:
- 生理层面的即时缓解（呼吸法、身体扫描、冷水刺激）
- 认知层面的重构技巧（灾难化思维识别、概率评估、最坏情况接受）
- 行为层面的应对（暴露疗法原理、渐进式脱敏、行为激活）
- 特定场景焦虑应对（社交焦虑、恋爱焦虑、职场焦虑、睡前焦虑）
- 长期焦虑管理习惯（运动、正念、减少咖啡因、社交支持）

- [ ] **Step 3: Create confidence-building.md**

Self-confidence building reference:
- 自信的认知模型（自信 ≠ 不紧张，自信 = 相信自己能应对）
- "能力自信"的建立方法（设定小目标→完成→积累证据）
- "社交自信"的建立方法（从低风险场景开始→逐步升级）
- "身体自信"的建立方法（运动、体态、形象管理的正反馈循环）
- 自信心日志：每天记录"今天我做到了什么"
- 冒充者综合征的识别和应对
- 自我对话改造（把"我不行"换成"我还在学"）

- [ ] **Step 4: Create attachment-theory.md**

Attachment theory in plain language:
- 四种依附类型通俗解读（安全型、焦虑型、回避型、混乱型）
- 自我评估：你是哪种类型？（通过行为描述而非量表）
- 每种类型在恋爱中的典型表现和困境
- 焦虑型→安全型的具体改善路径
- 回避型→安全型的具体改善路径
- 如何和不同依附类型的人相处

- [ ] **Step 5: Commit**

```bash
git add skills/inner-game/
git commit -m "feat: add inner-game skill — emotional resilience and confidence

- Anxiety toolkit with immediate and long-term strategies
- Confidence building framework with daily practices
- Attachment theory guide in plain language
- Scene-specific coping strategies"
```

---

### Task 5: Social-Voice Skill — Communication

**Files:**
- Create: `skills/social-voice/SKILL.md`
- Create: `skills/social-voice/references/conversation-skills.md`
- Create: `skills/social-voice/references/storytelling.md`
- Create: `skills/social-voice/references/workplace-communication.md`

- [ ] **Step 1: Create social-voice SKILL.md**

Create `skills/social-voice/SKILL.md` with:

- **Frontmatter**: name `social-voice`, description triggers on 不会聊天/找不到话题/冷场/怎么说服/演讲/面试/汇报/表达能力/沟通/说话技巧/嘴笨/不知道说什么/讲故事
- **Core capabilities**:
  1. 日常聊天：找话题/延伸话题/避免冷场/幽默感培养
  2. 讲故事：把经历讲得有趣的结构化方法
  3. 职场沟通：向上汇报/跨部门协作/会议发言/邮件写作
  4. 公众表达：演讲准备/PPT呈现/即兴发言
  5. 说服与谈判：表达观点/处理分歧/达成共识
  6. 倾听能力：主动倾听技巧/让对方觉得被理解
- **Response format**: 给出具体的话术模板和结构框架，附带"为什么这样说有效"的解释
- **Principles**: 真诚 > 技巧，倾听 > 表达，具体 > 空洞

- [ ] **Step 2: Create conversation-skills.md**

Daily conversation reference:
- 开启话题的 5 种万能方法
- 话题延伸的 3 种技巧（关键词发散/自我暴露/假设场景）
- 避免冷场的策略
- 幽默感的培养方法（不是背段子）
- 不同场景的聊天策略（初次见面/朋友聚会/一对一约会）
- 线上聊天 vs 面对面聊天的区别
- 聊天中的常见雷区

- [ ] **Step 3: Create storytelling.md**

Storytelling reference:
- 好故事的基本结构（STAR法/英雄之旅简化版/反转结构）
- 如何把日常经历讲得有趣（细节选择/节奏控制/情感曲线）
- 5 个万能故事模板（尴尬经历/旅行见闻/成长转折/有趣的人/意外发现）
- 讲故事时的声音和肢体语言配合
- 如何根据场景选择合适的故事
- 练习方法：每周准备一个新故事

- [ ] **Step 4: Create workplace-communication.md**

Workplace communication reference:
- 向上汇报的结构（结论先行/PREP法）
- 会议发言技巧（如何准备/如何即兴回应/如何收场）
- 跨部门沟通的原则（利益对齐/共情先行）
- 处理冲突的沟通框架（非暴力沟通简化版）
- 邮件/即时消息的写作原则
- 面试中的表达技巧
- 如何说"不"而不伤关系

- [ ] **Step 5: Commit**

```bash
git add skills/social-voice/
git commit -m "feat: add social-voice skill — communication and expression

- Conversation skills with topic starters and extension techniques
- Storytelling framework with templates and practice methods
- Workplace communication guide for reporting, meetings, emails
- Persuasion and active listening techniques"
```

---

### Task 6: Update Existing Skills — LevelUp Integration

**Files:**
- Modify: `skills/love-master/SKILL.md` (description only)
- Modify: `skills/alpacino-charm/SKILL.md` (description only)

- [ ] **Step 1: Update love-master description**

In `skills/love-master/SKILL.md`, update the frontmatter description to mention it's part of LevelUp and add cross-references to other skills where relevant (e.g., "如果你对自己的形象不自信，参考 image-craft 模块" at appropriate points in the body).

- [ ] **Step 2: Update alpacino-charm description**

In `skills/alpacino-charm/SKILL.md`, similarly update frontmatter and add cross-references where relevant (e.g., link to inner-game for confidence building, link to social-voice for voice training).

- [ ] **Step 3: Commit**

```bash
git add skills/love-master/SKILL.md skills/alpacino-charm/SKILL.md
git commit -m "feat: integrate existing skills into LevelUp ecosystem

- Update descriptions to reference LevelUp
- Add cross-references to other modules"
```

---

### Task 7: /levelup Command

**Files:**
- Create: `commands/levelup.md`

- [ ] **Step 1: Create levelup.md command**

Create `commands/levelup.md` with frontmatter and command logic:

```markdown
---
name: levelup
description: LevelUp 个人成长系统入口。用于启动诊断、查看计划、每周 check-in。
arguments:
  - name: action
    description: "start（开始诊断）、checkin（每周回顾）、plan（查看计划）、reset（重新设定目标）"
    required: false
---
```

Command body should:
- No argument or `start`: Check if user has a profile in memory. If not, trigger onboarding skill. If yes, show current status (goal, current phase, this week's focus).
- `checkin`: Trigger weekly check-in flow (ask about progress, obstacles, adjust plan).
- `plan`: Read and display the user's current growth path document.
- `reset`: Clear existing goal and re-trigger onboarding.

- [ ] **Step 2: Commit**

```bash
git add commands/levelup.md
git commit -m "feat: add /levelup command — main entry point

- /levelup or /levelup start: diagnosis or status
- /levelup checkin: weekly check-in
- /levelup plan: view growth path
- /levelup reset: restart goal setting"
```

---

### Task 8: Weekly Check-in Agent

**Files:**
- Create: `agents/weekly-checkin.md`

- [ ] **Step 1: Create weekly-checkin.md agent**

Create `agents/weekly-checkin.md` with frontmatter:

```markdown
---
name: weekly-checkin
description: LevelUp 每周成长回顾 agent。帮助用户复盘本周进展、识别障碍、规划下周重点。在用户使用 /levelup checkin 或提到"这周总结""回顾一下""本周复盘"时触发。
---
```

Agent body should define the check-in flow:
1. Read user's profile and current growth path from memory
2. Ask about this week's actions (what did you do from the plan?)
3. Ask about obstacles (anything that blocked you?)
4. Ask about highlights (any wins or interesting encounters?)
5. Give feedback and encouragement based on progress
6. Suggest adjustments if needed
7. Set next week's top 3 priorities
8. Update progress in memory

- [ ] **Step 2: Commit**

```bash
git add agents/weekly-checkin.md
git commit -m "feat: add weekly check-in agent

- Structured weekly review flow
- Progress tracking and memory updates
- Adaptive plan adjustment
- Encouragement and obstacle identification"
```

---

### Task 9: README and Documentation

**Files:**
- Create: `docs/README.md`
- Create: `README.md` (project root, symlink or copy)

- [ ] **Step 1: Create README.md**

Create `README.md` at project root with:

- Project name and one-line description
- What LevelUp is (目标驱动的男性个人成长 AI 教练)
- Feature overview (6 modules, goal-driven paths, weekly check-ins)
- Installation instructions (how to install as Claude Code plugin)
- Quick start guide (/levelup start)
- Module list with brief descriptions
- Screenshots/example interactions (text-based examples)
- Contributing guidelines (basic)
- License

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add project README with installation and usage guide"
```

---

### Task 10: Final Integration and Push

- [ ] **Step 1: Verify complete plugin structure**

```bash
find . -type f -not -path '*/.git/*' -not -path '*/love-master-workspace/*' -not -path '*/personal/*' -not -path '*/node_modules/*' | sort
```

Verify all expected files exist per the spec.

- [ ] **Step 2: Test plugin loads**

```bash
# Verify plugin.json is valid JSON
cat .claude-plugin/plugin.json | python3 -m json.tool
```

- [ ] **Step 3: Final commit and push**

```bash
git add -A
git status
# If there are any uncommitted changes:
git commit -m "chore: final v1.0 cleanup"
git push
```

- [ ] **Step 4: Tag v1.0**

```bash
git tag -a v1.0.0 -m "LevelUp v1.0.0 — 男生版自我提升操作系统

Modules: onboarding, love-master, alpacino-charm, image-craft, inner-game, social-voice
Features: goal-driven paths, weekly check-ins, /levelup command"
git push origin v1.0.0
```
