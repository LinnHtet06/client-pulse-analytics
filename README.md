# CLIENT//PULSE — Service Operations Analytics

> An end-to-end data analyst case study on **client service operations** for a fictional financial services firm, created for study purposes and presented as an interactive, 3D, futuristic web page.

Most portfolio projects about financial firms look at stock prices or investment returns. This one looks at how the firm serves its clients instead: **when clients get in touch, which channels they use, where they get stuck, and what actually affects their satisfaction.**

🔗 **Live demo:** open `index.html` in a browser, or enable GitHub Pages for this repo.

---

## 🎯 Business questions

| # | Question | Why it matters |
|---|----------|----------------|
| 01 | When is support demand highest? | Staffing should follow real demand |
| 02 | How are clients switching channels? | Plan the move from phone and branch to chat and the app |
| 03 | Which request types cause the most friction? | Shows where to fix processes |
| 04 | What drives customer satisfaction (CSAT)? | Shows where improvements pay off most |

## 🗂️ Dataset

**24,000 support tickets** covering Jan–Dec 2025, across **5 channels** and **6 request types**.

| Field | Type | Description |
|-------|------|-------------|
| `ticket_id` | int | Unique contact ID |
| `month`, `weekday`, `hour` | datetime parts | When the client got in touch |
| `channel` | category | Phone, Email, Live Chat, Mobile App, Branch |
| `category` | category | Account Access, Card Services, Onboarding / KYC, App Issue, Statements & Docs, Complaints |
| `first_response_min` | float | Minutes until the first reply |
| `resolution_hrs` | float | Hours from open to close |
| `escalated` | bool | Passed to tier-2 / a specialist |
| `repeat_contact` | bool | Client came back within 7 days |
| `csat` | int (1–5) | Post-contact survey score |

> ⚠️ **The data is synthetic.** It is generated in the browser with a fixed random seed (42), so every number can be reproduced. You can download the full dataset as a CSV from the page.

## 🔬 Method

1. **Extract:** pull tickets from the CRM and the survey tool
2. **Clean:** remove duplicates, fix timezones, cap outliers
3. **Feature engineering:** peak-hour flag, response-time buckets
4. **Exploratory analysis:** volume trends, channel mix, weekday × hour heatmap, friction by request type
5. **Driver analysis:** Pearson correlation of each factor with CSAT
6. **Impact sizing:** estimate the effect of each recommendation

```sql
-- repeat-contact rate by category
SELECT category,
       COUNT(*)                         AS tickets,
       AVG(repeat_contact::int)         AS repeat_rate,
       PERCENTILE_CONT(0.5)
         WITHIN GROUP (ORDER BY resolution_hrs) AS median_resolution
FROM service.tickets
GROUP BY category
ORDER BY repeat_rate DESC;
```

## 📊 Key findings

| KPI | Value |
|-----|-------|
| Total contacts | 24,000 |
| Median first response | 6.3 min |
| Average CSAT | 3.86 / 5 |
| First-contact resolution | 87.4% |
| Escalation rate | 11.6% |

- ⏱️ **Demand is concentrated.** Monday and Tuesday from 09:00 to 11:59 is only 6 of the week's 168 hours, but it brings in **12.3%** of all tickets.
- 📱 **Clients are moving to digital.** Live chat and the mobile app gain share every month, while phone and branch decline.
- 🔁 **Complaints cause the most friction.** They have the highest repeat-contact rate (**30.4%**) and the lowest CSAT (**3.49**).
- ⚡ **Reply speed is the strongest CSAT driver.** Replies under 5 minutes score **+0.95 CSAT** higher than replies that take over 4 hours. Escalation costs **−0.78**, and clients who have to come back average only **3.26**.

## 💡 Recommendations

| Action | Estimated impact |
|--------|------------------|
| **Peak-hour staffing:** move 3–4 agents from Thursday and Friday afternoons to Monday and Tuesday mornings, and add a chat callback queue | ≈ 2,956 peak tickets/yr answered faster |
| **Self-service statements:** let clients download statements and documents in the app (assumes 60% of these requests are deflected) | − 2,405 tickets/yr |
| **Release QA gate:** staged rollouts and a crash-rate kill-switch, after the app v5 release caused a spike in App Issue tickets | − 835 avoidable tickets |

## ✨ Presentation

The findings are presented as a single-page site:

- **Three.js** 3D globe of client nodes, with glowing arcs carrying "tickets", orbiting rings and floating shapes. The camera follows the mouse and moves as you scroll.
- **Chart.js** charts (stacked area, horizontal bar, bubble) that animate when they scroll into view
- An interactive weekday × hour **heatmap** that shows details on hover
- Glass panels, cards that tilt in 3D on hover, numbers that count up, a scroll progress bar, and a neon grid floor
- Respects the `prefers-reduced-motion` setting

## 🛠️ Tech stack

`SQL` · `Python / pandas` (analysis approach) · `Statistics` · `JavaScript` · `Three.js r128` · `Chart.js 4.4` · `HTML / CSS`

## 🚀 Run it locally

The page is a single HTML file with no build step:

```bash
# any static server works, for example:
python -m http.server 8000
# then open http://localhost:8000
```

Or just double-click `index.html`. It needs an internet connection to load Three.js, Chart.js and the fonts.

## 📁 Structure

```
├── index.html   # the whole project: data generation, analysis and 3D presentation
└── README.md
```

---

**Disclaimer:** This project was created for study purposes only. The firm is fictional, all data is synthetic, and no real company or client data is used.
