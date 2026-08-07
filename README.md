# Handoff: 한국사 유물 시대 맞추기 (Korean History Artifact Quiz & Encyclopedia)

## Overview
A Korean-history study web app for students preparing for 수능 / 한국사능력검정시험. Users learn ~90 historical artifacts through three modes:
- **퀴즈 (Quiz)** — shows an artifact photo; the user answers **two** questions per card: ① which **era** and ② the artifact **name**. 10 random cards per session.
- **유물 도감 (Encyclopedia / Gallery)** — a filterable card grid; tap a card to expand full study notes.
- **타임라인 (Timeline)** — artifacts arranged chronologically by era, grouped by nation within each era.

## About the Design Files
The files in this bundle are a **design reference created in HTML** — a working prototype showing the intended look, content, and behavior. They are **not** meant to be shipped as-is. The task is to **recreate this app in the target codebase's environment** (React, Next, Vue, native, etc.) following that codebase's established patterns, then port the data and interactions described below.

The prototype is a single **Design Component** file (`유물시대퀴즈.dc.html`) built on a proprietary runtime (`support.js`) — a lightweight React-based template engine. **Do not depend on `support.js` in production.** Treat the `.dc.html` as a spec: the logic class (`class Component`) is plain React-style class-component logic you can lift almost verbatim; the template is inline-styled markup you'll re-express in your framework.

## Fidelity
**High-fidelity.** Final colors, typography, spacing, and interactions are all decided. Recreate the UI pixel-accurately using the codebase's libraries. All copy (Korean) is final and should be ported exactly.

## Data Model
The heart of the app is a single array, `this.artifacts`, in the logic class. Each entry:

```js
{
  name: '금동연가7년명여래입상',      // artifact name (also a quiz answer)
  period: '삼국시대 · 고구려',         // sub-label shown under name
  era: '삼국·가야',                    // one of the 7 eras (quiz answer + grouping key)
  photo: 'assets/xxx.png',            // OPTIONAL. If absent → "사진 준비 중" placeholder, and the card is EXCLUDED from the quiz pool
  desc: '…',                          // one-paragraph summary
  detail: {                           // OPTIONAL richer study notes
    key: '한 줄 암기 요약',             // "이것만 기억" one-line takeaway
    sections: [
      { label: '기본 정보', text: '…' },
      { label: '시험 함정', text: '① … ② … ③ …' }  // ①②③ auto-split onto separate lines
    ]
  }
}
```

The 7 eras, in order:
```
['선사시대','고조선·초기국가','삼국·가야','통일신라·발해','고려','조선','근현대']
```

**Nation grouping** (within an era) is derived, not stored:
- `삼국·가야` splits into `고구려 → 백제 → 신라 → 가야` (matched from the `period` string).
- `통일신라·발해` splits into `통일신라 → 발해`.
- All other eras render as a single group.

Recommendation for the real codebase: move `artifacts` into a standalone data file (JSON/TS) — it is the app's core content and should be editable without touching UI code. ~90 entries currently; the full list lives in `유물시대퀴즈.dc.html` (`this.artifacts = [...]`).

## Screens / Views

### Shared chrome
- **Header**: title `유물로 배우는 한국사` (Noto Serif KR, 700, 21px, `#2A2622`) + subhead (12.5px, `#8a7f68`). On the quiz tab only, a right-aligned score pill (`점수 N / M`).
- **Tab bar**: 3 pill buttons (`퀴즈`, `유물 도감`, `타임라인`) in a rounded container. Active tab = dark fill `#2A2622` / cream text `#F4EFE2`; inactive = transparent / `#8a7f68`.
- Max content width 600px (quiz) / 720px (gallery, timeline), centered.

### 1. 퀴즈 (Quiz)
- **Deck**: on each fresh start (mount or "다시 풀기"), shuffle all artifacts that have a `photo`, take the first **10**.
- **Progress**: `문제 {current} / 10` + a progress bar filled by how many cards have BOTH questions answered.
- **Artifact panel**: hatched cream background (`repeating-linear-gradient(45deg,#F6F2E8,#F6F2E8 14px,#F3EEE2 14px,#F3EEE2 28px)`), `ARTIFACT` label top-left, centered `<img>` 250×250 `object-fit:contain` with drop shadow.
- **Two question blocks**, each numbered with a circled badge (`#4C7A6B` fill):
  - ① `어느 시대의 것일까요?` — 4 era options.
  - ② `유물의 이름은 무엇일까요?` — 4 name options.
- **Options**: full-width buttons, cream `#FCFAF5`, border `#E0D8C4`, 16.5px/600. Hover (unanswered): bg `#F3EEE0`, border `#c9bd9f`. After a choice is locked (per question): correct → green (`bg #EAF3EC`, border `#3E7C57`, text `#2C5B40`, `✓`); wrong pick → red (`bg #F7ECE7`, border `#B4472F`, text `#8f331f`, `✕`); others dimmed to opacity .45. Each question locks independently on first tap.
- **Reveal card** (after BOTH answered): tan panel `#F6F2E8`/border `#E8E0CE` with artifact name (Noto Serif KR 700 19px), period sublabel, and `desc` paragraph.
- **Nav row**: `← 이전` (disabled/greyed on first card), `다시 풀기` (outline), `다음 →` (dark; label becomes `결과 보기` on last card). Going back preserves previously chosen answers and the revealed state.
- **Result screen**: `QUIZ COMPLETE` eyebrow, message keyed to % (≥90 `유물 박사예요!`, ≥70 `아주 잘하고 있어요`, ≥50 `조금만 더 힘내요`, else `다시 도전해봐요`), big score `N / 20` (2 questions × 10 cards), `정답률 %`, and a `다시 풀기` button.

### 2. 유물 도감 (Encyclopedia / Gallery)
- **Filter chips**: `전체` + one per era. Active = `#4C7A6B` fill / white; inactive = cream / `#6f6550`.
- **Sections**: grouped by era, then by nation. Section header = green label + hairline rule. In `전체` view header reads e.g. `삼국·가야 · 백제`; under a specific era filter it reads just the nation.
- **Card grid**: `repeat(auto-fill, minmax(158px, 1fr))`, 14px gap. Card = cream, border `#E4DECF`, radius 16, subtle shadow; hover lifts (`translateY(-2px)` + stronger shadow).
  - Collapsed: hatched thumbnail (img 110×110), name (Noto Serif KR 700 15px), era chip (green) + period chip (tan) + optional `사진 준비 중` chip (amber `#F6EDD8`/`#e6d3a8`/`#b1802f`), and hint `눌러서 설명 보기 →`.
  - Expanded (tap toggles): image enlarges (up to 320×280); `desc` paragraph shows; if `detail` exists, the card spans the full grid width (`gridColumn: 1 / -1`) and shows all `detail.sections` (each label in green uppercase, body split by ①②③ into separate lines) plus a dark "이것만 기억" takeaway box (bg `#2A2622`, label `#E3BA46`, text `#F4EFE2`).
- Gallery and Timeline share one `expanded` state (same artifact stays open across tabs).

### 3. 타임라인 (Timeline)
- Vertical spine: per era, a dot (`#4C7A6B`, 3px cream ring) + connecting line, era title (Noto Serif KR 700 18px) to the right.
- Within an era, nation groups each get a green pill label (`고구려`, `백제`, …), then a wrapping row of 150px cards (hatched thumb + name + period).
- Tapping a timeline card expands it in place: it widens to full width, switches to a horizontal row (enlarged image left ~240×260, text right), and shows `desc` + `detail` sections + takeaway box — identical content treatment to the gallery.

## Interactions & Behavior
- **Tab switch**: instant; content fades up (`@keyframes fadeup`, ~.3s).
- **Quiz answer**: per-question lock on first click; no un-answering. Score = count of correct era answers + correct name answers.
- **Distractor logic**:
  - Era options: the correct era plus the 3 *nearest* eras by index distance (so choices are plausibly close in time). Shuffled.
  - Name options: 3 distractors preferring **same-era** artifacts first, then other eras. Shuffled.
- **Drag guard**: a pointer move >8px between pointerdown and click suppresses the card toggle (so selecting/scrolling text doesn't collapse the card).
- **Placeholder**: artifacts without `photo` render an inline SVG "사진 준비 중" placeholder and are excluded from the quiz pool but fully visible in gallery/timeline.
- **Card expand/collapse**: single `expanded` = artifact name or null; tapping an open card closes it.

## State Management
Component state:
- `mode`: `'quiz' | 'gallery' | 'timeline'`
- `deck`: array of 10 quiz cards (each = artifact + precomputed `eraOptions`, `nameOptions`)
- `idx`: current quiz card index
- `answers`: `{ [cardIndex]: { era?: string, name?: string } }`
- `finished`: boolean (quiz result screen)
- `filter`: `'all'` or an era name (gallery)
- `expanded`: artifact name or null (gallery+timeline shared)

Derived per render: score, answered-count, progress %, nation groupings, option button styles. No data fetching — all content is static/in-memory. No persistence currently (a nice-to-have: persist high score / last mode in localStorage).

## Design Tokens
Colors:
- Page bg gradient: `radial-gradient(120% 90% at 50% 0%, #F1ECDF 0%, #E4DDCC 100%)`; body `#E9E3D4`
- Surface / card: `#FCFAF5`; card border `#E4DECF` / `#E0D8C4`
- Ink (primary text): `#2A2622`; body text `#514a3c`; muted `#8a7f68`, `#a2977d`, `#b0a488`
- Accent green (primary): `#4C7A6B` (hover `#3a5f53`); light green chip bg `#EAF1EE` / border `#d5e5df`
- Dark button / takeaway: `#2A2622` (hover `#3d372e`), text `#F4EFE2`, gold label `#E3BA46`
- Correct: bg `#EAF3EC`, border `#3E7C57`, text `#2C5B40`
- Wrong: bg `#F7ECE7`, border `#B4472F`, text `#8f331f`
- Amber "사진 준비 중": bg `#F6EDD8`, border `#e6d3a8`, text `#b1802f`
- Hatched artifact bg: `repeating-linear-gradient(45deg,#F6F2E8,#F6F2E8 14px,#F3EEE2 14px,#F3EEE2 28px)`

Typography:
- Display / titles / artifact names: **Noto Serif KR** (600–700)
- UI / body: **Pretendard** (regular–700), fallback system sans
- Sizes in use: 11–12.5px (labels/chips), 13.5–14.5px (body), 15–18px (buttons/prompts), 19–26px (names/results), 56px (result score)

Radius: chips/pills 999px; buttons 11–12px; cards 14–18px.
Shadows: cards `0 2px 8px rgba(74,63,40,.06)`, hover `0 8px 20px rgba(74,63,40,.12)`, panels `0 6px 22px rgba(74,63,40,.08)`.
Animation: `fadeup` (translateY 10px + opacity, ~.3s ease).

## Assets
`assets/` contains ~110 PNG artifact photos supplied by the user (referenced by each artifact's `photo` field; filenames are a mix of Korean and romanized names). These are the user's own study images — verify redistribution rights before publishing. Artifacts without a matching photo intentionally show the placeholder.

## Files
- `유물시대퀴즈.dc.html` — the full prototype: template markup + `class Component` logic (the authoritative source for data, styles, and behavior).
- `support.js` — the prototype runtime (reference only; do not ship).
- `assets/` — artifact photos.
