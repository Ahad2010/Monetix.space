# DESIGN.md — Momentix.space Design System (Source of Truth)

> Brand: Momentix.space | Tagline: Work Smarter. Grow Faster.
> Style: Premium, clean, modern B2B AI SaaS | Primary mode: Light

This file is authoritative. Any UI component (Codex, marketing site, dashboard) must be built to these tokens exactly — no ad hoc colors, spacing, or radii. If a value is needed that isn't here, add it to this file first, then use it.

## 1. Typography

Font: **Inter**, `font-family: "Inter", sans-serif;` — used everywhere (dashboard, marketing, forms, tables, buttons, nav, mobile).

Weights: 400 Regular (body), 500 Medium (nav/labels/inputs), 600 SemiBold (buttons/card titles/H3-H4), 700 Bold (H1/H2/important numbers), 800 ExtraBold (hero/marketing headings only).

**Headings (desktop)**
| Element | Size | Weight | Line height | Letter spacing |
|---|---|---|---|---|
| H1 | 36px | 700 | 44px | -0.03em |
| H2 | 30px | 700 | 38px | -0.025em |
| H3 | 24px | 600 | 32px | -0.02em |
| H4 | 20px | 600 | 28px | -0.015em |
| H5 | 16px | 600 | 24px | -0.01em |
| H6 | 14px | 600 | 20px | 0 |

**Dashboard type scale**: Page Title 30/700, Section Title 20/600, Card Title 16/600, Large Metric 28/700, Body 14/400, Body Medium 14/500, Small 13/400, Label 12/500, Badge 12/600, Button 14/600, Navigation 14/500.

**Mobile type scale**: Page Title 26/700, Section Title 18/600, Card Title 15/600, Large Metric 24/700, Body 14/400, Small 12/400, Button 14/600.

## 2. Brand Colors

- Primary — Momentix Indigo `#6366F1`: primary buttons, active nav, links, selected controls, focus states, important icons.
- Secondary — Momentix Blue `#3B82F6`: secondary highlights, charts, AI states, gradients.
- Brand gradient: `linear-gradient(135deg, #6366F1 0%, #3B82F6 100%)` — use sparingly (logo, AI icon, premium CTA, hero accents, loading states). Never everywhere.

## 3. Neutral Colors

```
Background          #F8FAFC
Surface / Card       #FFFFFF
Surface Secondary   #F1F5F9
Primary Text         #0F172A
Secondary Text       #475569
Muted Text           #64748B
Disabled Text        #94A3B8
Border               #E2E8F0
Border Light         #F1F5F9
```

## 4. Semantic Colors

| Type | Primary | Background | Text |
|---|---|---|---|
| Success | #10B981 | #ECFDF5 | #047857 |
| Error/Danger | #EF4444 | #FEF2F2 | #B91C1C |
| Warning | #F59E0B | #FFFBEB | #B45309 |
| Info | #3B82F6 | #EFF6FF | #1D4ED8 |
| AI/Intelligence | #7C3AED | #F5F3FF | #6D28D9 |

All error states in the product (including inline error banners required by RULES.md) use the Error/Danger tokens above — never a raw browser alert style.

## 5. Lead Score Colors

```
90–100  Hot Lead    #10B981
70–89   Good Lead   #3B82F6
50–69   Medium      #F59E0B
0–49    Low Match   #94A3B8
```
Never rely on color alone — always pair with a label or numeric score.

## 6. Buttons

- **Primary**: bg `#6366F1`, text white, height 40px, padding 0 16px, radius 8px, font 14/600. Hover: `#4F46E5`.
- **Secondary**: bg white, text `#0F172A`, border `#E2E8F0`, height 40px, radius 8px.
- **Destructive**: bg `#EF4444`, text white.
- **AI Premium CTA**: may use the brand gradient.

## 7. Border Radius

```
Small controls   6px
Buttons/Inputs   8px
Cards            12px
Large panels     16px
Modal            16px
Avatar           50% / full
Pills            9999px
```
Avoid over-rounding every element.

## 8. Spacing

Base unit: 4px → 4, 8, 12, 16, 20, 24, 32, 40, 48, 64.

Common usage: card padding 20–24px, dashboard gap 16–24px, section gap 32px, input gap 8px, page horizontal padding 24–32px, mobile page padding 16px.

## 9. Cards

Default: bg `#FFFFFF`, border `1px solid #E2E8F0`, radius 12px, padding 20px, shadow `0 1px 3px rgba(15, 23, 42, 0.06)`. Hoverable cards may slightly increase border/shadow — avoid large floating shadows.

## 10. Inputs & Search

Height 40–44px, bg white, border `#E2E8F0`, radius 8px, text `#0F172A`, placeholder `#94A3B8`. Focus: border `#6366F1`, focus ring `rgba(99, 102, 241, 0.15)`. Global AI/search command bar: 48–52px height.

## 11. Sidebar (desktop)

Width 240–260px, bg `#0F172A`, primary text white, muted text `#94A3B8`, active bg `rgba(99, 102, 241, 0.16)`, active text white, active accent `#6366F1`.

Nav items: Home, Inbox, Leads, Contacts, Tasks, Workflows, Research, Calendar, Files, Analytics, Integrations, Settings.

**Mobile**: bottom nav with Home, Inbox, Leads, Tasks, More (rest live inside More).

## 12. Top Navigation

Global Search/Ask AI, Notifications, + New, Profile, Plan. Height 64px. White background, subtle bottom border.

## 13. AI Components

Use Indigo `#6366F1`, Blue `#3B82F6`, AI Purple `#7C3AED`. Components: Ask AI command bar, AI Suggested Actions, AI-generated draft badge, Research status, AI lead-score explanation, AI assistant, AI loading state. Suggested badge text: `✦ AI Generated`. Never make the whole app purple — AI color is an accent only.

## 14. Notifications

Types → color: Email blue, Lead green, AI Action purple, Follow-up orange, Error red, Billing indigo, Calendar blue.

Notification card: icon, title, description, time, unread indicator (`#6366F1`), optional CTA.

## 15. Inbox UI

Email rows: avatar/company icon, sender, subject, short preview, category badge, priority, time, AI action. Category badges: New Lead, Client, Follow-up, Important, Invoice, Noise. Selected row: light indigo background, not a heavy border.

## 16. Lead Finder UI

Lead card/row shows: company, location, match score, website, opportunity signals, status, and actions [Research] [Save Lead] [Generate Outreach]. Uses Lead Score Colors (Section 5).

## 17. AI Suggested Actions

Card format: "AI Suggested Action" header, short title, one-line context, then [Approve] [Edit] [Reject]. Approve = success green (confirms an action). Edit = neutral. Reject = subtle unless destructive.

## 18. Tables

Header bg `#F8FAFC`, header text `#64748B`, body text `#0F172A`, row border `#F1F5F9`, selected row `#EEF2FF`. Font 13–14px. Row height 48–56px.

## 19. Charts & Analytics

Primary series `#6366F1`. Supporting series: `#3B82F6`, `#10B981`, `#F59E0B`, `#7C3AED`. Keep charts simple and readable. Dashboard metrics to visualize: AI Actions Completed, Leads Found, Emails Handled, Follow-ups Sent, Estimated Time Saved, Conversion Rate, AI Usage.

## 20. Icons

Lucide Icons only. Stroke width ~1.75–2px. Default size 18–20px, small 16px, large 24px. One icon family throughout — no mixing.

## 21. Responsive Breakpoints

Mobile <640px, sm 640px+, md 768px+, lg 1024px+, xl 1280px+, 2xl 1536px+. Design desktop-first, fully functional on mobile.

## 22. Mobile UX Rules

Sidebar → bottom nav/drawer. Cards → single column. Tables → compact cards where needed. Primary CTA stays reachable. AI command bar stays prominent. Modals may become bottom sheets/full-screen. Minimum touch target ~44px. No horizontal scroll on primary workflows. Inbox, Leads, Tasks, Approvals must be fully usable on mobile.

## 23. Layout Width

Marketing max width 1200–1280px. Dashboard: sidebar 240–260px, main content flexible, page padding 24–32px, optional large-screen max 1600px+. Data-heavy screens may use full available width.

## 24. Motion

Hover 150–200ms, modal 200–250ms, dropdown 150–200ms, page element entrance 200–300ms. Framer Motion, used selectively. Avoid floating/bouncing/continuously-moving elements.

## 25. Logo Usage

**Final logo confirmed** (matches this design system exactly — no token changes needed):

- **Mark**: a stylized "M" made of two overlapping ribbon/wave shapes, diagonal gradient from Indigo `#6366F1` (left) to Blue `#3B82F6` (right), soft rounded ends, subtle inner shading for depth. A small solid Indigo dot sits above the "i" in the wordmark as a brand accent (echoes the "™" placement).
- **Wordmark**: "Momentix" in Inter ExtraBold (800), tight letter spacing, set in `#0F172A` (dark) on light backgrounds or white on dark backgrounds. Tagline "Work Smarter. Grow Faster." in Inter Regular, muted gray, tracked out slightly, sits directly under the wordmark.
- **Confirmed palette** (from final logo art — matches `DESIGN.md` tokens 1:1):
  - Primary `#6366F1`
  - Secondary `#3B82F6`
  - Dark `#0F172A`
  - Light `#E5E7EB` (near-identical to existing `--border: #E2E8F0` — treat as the same neutral-light role, no new token needed)

Required variants (all produced from the same mark + these exact colors):
- Full logo — light background (dark wordmark, mark at full gradient)
- Full logo — dark background (white wordmark, mark at full gradient)
- Icon only (mark on a rounded-square dark `#0F172A` tile — used as app icon)
- Favicon (icon-only mark, simplified for small sizes)
- Monochrome (single-color mark for stamps/watermarks where gradient isn't supported)
- Mobile/app icon (icon-only mark on `#0F172A` rounded-square, per the App Icon reference)

This is the single source of truth for all logo placements — dashboard header, favicon, marketing site, app icon, loading states, and any AI-gradient accents in `DESIGN.md` §13 all pull from this exact mark and palette.

## 26. CSS Design Tokens

```css
:root {
  --momentix-primary: #6366F1;
  --momentix-primary-hover: #4F46E5;
  --momentix-secondary: #3B82F6;
  --momentix-ai: #7C3AED;

  --background: #F8FAFC;
  --surface: #FFFFFF;
  --surface-secondary: #F1F5F9;

  --text-primary: #0F172A;
  --text-secondary: #475569;
  --text-muted: #64748B;
  --text-disabled: #94A3B8;

  --border: #E2E8F0;
  --border-light: #F1F5F9;

  --success: #10B981;
  --warning: #F59E0B;
  --danger: #EF4444;
  --info: #3B82F6;

  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --radius-xl: 16px;
}
```

## 27. Tailwind Brand Tokens

```
momentix-50   #EEF2FF
momentix-100  #E0E7FF
momentix-500  #6366F1
momentix-600  #4F46E5

blue-500      #3B82F6
ai-600        #7C3AED

slate-50      #F8FAFC
slate-100     #F1F5F9
slate-200     #E2E8F0
slate-500     #64748B
slate-600     #475569
slate-900     #0F172A
```

## 28. Component Consistency Rules

1. Inter is the default UI font.
2. Indigo is the primary interaction color.
3. Blue/purple gradients are reserved for brand/AI moments.
4. White cards sit on a very light slate background.
5. Borders preferred over heavy shadows.
6. Green = success/approved.
7. Red = errors/destructive only.
8. Orange = warnings/follow-ups.
9. One consistent icon family (Lucide).
10. Desktop and mobile share the same visual language.
11. Data-heavy screens prioritize readability over decoration.
12. Every AI action clearly communicates what will happen before execution.

## 29. Visual Personality

Should feel: Intelligent · Fast · Trustworthy · Premium · Focused · Modern.
Should NOT feel: Overly futuristic · Neon-heavy · Gaming-like · Cluttered · Cartoonish · Over-animated.

> A professional workspace first, with intelligence built into every interaction.

## 30. Final Brand Cheat Sheet

```
Brand             Momentix.space
Tagline           Work Smarter. Grow Faster.
Font              Inter

Primary           #6366F1
Primary Hover     #4F46E5
Secondary         #3B82F6
AI Purple         #7C3AED

Dark              #0F172A
Background        #F8FAFC
Card              #FFFFFF
Border            #E2E8F0

Success           #10B981
Warning           #F59E0B
Danger            #EF4444

Card Radius       12px
Button Radius     8px
Input Radius      8px

Desktop Sidebar   240–260px
Topbar            64px
Mobile Padding    16px
Desktop Padding   24–32px

Icon System       Lucide
Primary Mode      Light
```
