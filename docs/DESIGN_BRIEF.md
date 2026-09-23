# Design Brief — Retail Gadget Executive Dashboard

Design context for the Power BI Report Authoring agent. Read this together with
`MODEL_SCHEMA.md`: the schema defines *what data* to show, this brief defines
*how it should look*.

---

## Mood & intent

Modern, clean, corporate-but-not-stiff. The dashboard is for executives who need
to read the numbers fast, so readability and clear hierarchy win over decoration.
One screen, no scrolling.

## Color palette

| Role | Hex | Usage |
|---|---|---|
| Primary | `#0A2540` | Header bar, titles, slicer panel background |
| Accent | `#FF6B35` | KPI highlights, trend line, primary call-outs |
| Secondary | `#2EC4B6` | Second data series, category accents |
| Positive | `#2EC4B6` | Growth / favorable values |
| Negative | `#E63946` | Decline / unfavorable values (e.g. margin drop) |
| Background | `#F4F6F8` | Page canvas |
| Card | `#FFFFFF` | Visual/card backgrounds |
| Text secondary | `#6B7280` | Labels, captions, axis text |

## Typography

- Titles & KPI figures: **Segoe UI Semibold**
- Body text & labels: **Segoe UI Regular**
- KPI numbers large (28–32pt); small uppercase label above each (10pt, grey `#6B7280`)

## Layout

- **Top row:** 4 KPI cards — Total Sales, Total Profit, Profit Margin %, Total Orders (each with a small YoY% indicator)
- **Second row:** wide line chart (left, sales trend by month) + bar chart (right, sales by category)
- **Third row:** map (left, sales by city) + donut/bar (right, contribution by segment)
- **Left rail:** thin slicer panel on the dark primary background — Year, Island, Segment

## Style rules

- Rounded card corners (~8px), soft shadow — avoid heavy borders
- Generous whitespace between visuals; do not crowd
- Number format: short Rupiah (e.g. `Rp 27.5 M`), percentages to 1 decimal
- Consistent alignment to a grid; titles left-aligned above each visual
- Keep chart junk minimal — no unnecessary gridlines, legends only where needed

## What to avoid

- Default Power BI theme colors
- Cluttered visuals or more than ~7 visuals on the page
- 3D effects, gradients-as-decoration, or clashing accent colors
