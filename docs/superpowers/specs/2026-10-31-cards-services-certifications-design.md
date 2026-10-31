# Rounded cards, Services section and certifications: design

Date: 2026-10-31
Files changed: `index.html`, `style.css`, `README.md`, `CLAUDE.md`, two new badge images

## Goal

Add the owner's Cisco certifications and CRM skills to the portfolio, add a Services section, and restyle the site's boxes to match the reference portfolio (the "Vanshika" site): rounded cards with thin borders and small rounded chips inside.

The reference supplies the box shape and inner layout only. The site keeps its slate and emerald colours, its fonts and its ocean backdrop.

## 1. Box style, site-wide

The stylesheet resets every element to `border-radius: 0` on line 36, so each rounded element needs an explicit radius.

**Tokens.** Add to `:root`: `--radius: 14px` for cards and `--radius-sm: 8px` for chips, buttons, tags and badges.

**Cards using `--radius`:** `.project-card`, `.stack-group`, `.tl-card`, `.focus-card`, `.think-card`, `.about-stat`, `.gh-stat`, `.gh-card`, `.repo-card`, `.terminal`, `.hero-photo`, and the new `.service-card` and `.cert-card`.

**Small elements using `--radius-sm`:** all chips (the shared chip rule plus the new chip lists), `.btn`, `.project-link`, `.project-tag`, `.badge`, `.contact-link`, `.terminal-toggle`, and the hover label on skill chips.

**Fully round marks:** level dots, `.lang-name::before`, timeline nodes and `.terminal-dot`.

**Unchanged:** the header, nav, loader and skip link.

No shadows are added. Card fills stay opaque `--bg-card`, with no `backdrop-filter`, in line with the site's performance constraint.

**Corner handling, which is required for the look to hold:**

- `.glow::after` and `.glow::before` get `border-radius: inherit`, so the cursor glow follows the rounded edge.
- `.glow` boxes can't use `overflow: hidden`, so inner elements need their own corners:
  - `.project-img-wrapper` gets top corners of `calc(var(--radius) - 1px)`.
  - `.terminal-bar` gets the same top corners.
  - `.hero-photo img` and `.hero-caustics` get `calc(var(--radius) - 8px)`, because the photo box has 8px padding.

## 2. Inner layouts, following the reference

The reference pattern is a heading, a description, a thin divider, then chips.

| Box | Change |
| --- | --- |
| Skills (`.stack-group`) | Already chips. Move `.stack-note` above the chip list as the description, and add the divider above the chips. |
| Case Files (`.project-card`) | Add a divider above `.project-stack`. The links stay at the bottom. |
| Focus (`.focus-card`) | Replace the `.focus-detail` dot-separated text with a `.focus-tags` chip list, keeping its divider. |
| Journey (`.tl-card`) | Add a divider above `.tl-tags`. |
| Approach (`.think-card`) | Turn `.think-num` into a small rounded tile holding "01", like the reference's numbered tiles. |

The skill level dots stay inside the chips, along with the legend and the "honest self-assessment" line. This keeps Learning-level skills from reading the same as Confident ones.

## 3. CRM skills

Add a seventh `.stack-group` to the Stack section:

- **Heading:** CRM & business tools
- **Chips:** Zoho and Odoo, both `data-level="growing"`, each with its `sr-only` level span
- **Note:** "Customers, pipelines and follow-ups."

**Grid layout.** Seven cards would leave a lone card in the last row of three columns, so two cards span wider:

- Three columns: `.stack-primary` (Backend) and Tools & ops (new class `.stack-wide`) each span 2, filling all three rows.
- Up to 900px, two columns: `.stack-wide` resets to span 1 while Backend still spans 2, which leaves no gap.
- Up to 600px, one column: `.stack-primary` resets to span 1. Without this reset, the span would create a stray second column.

Also update the terminal `stack` command by adding a `CRM: Zoho, Odoo` line, and add Zoho and Odoo to the README stack line.

## 4. Certifications

Add a block inside `#stack`, directly under `.stack-grid`:

- A `.mini-label` reading "Certifications"
- A `.cert-grid` of two `.cert-card glow reveal` cards, side by side, stacking to one column at 600px

| Title | Issuer | Date | Image |
| --- | --- | --- | --- |
| Data Science Essentials with Python | Cisco Networking Academy | Issued Oct 28, 2026 | `Assets/images/cert-data-science.png` |
| Introduction to Cybersecurity | Cisco Networking Academy | Issued Oct 28, 2026 | `Assets/images/cert-cybersecurity.png` |

Each card shows the badge at 72px on the left, with the title, issuer and date on the right. Each `<img>` gets `width`, `height`, `loading="lazy"` and alt text naming the certificate.

**Badge images.** Use the owner's original badge files if supplied. Otherwise crop them from the owner's screenshot, where each badge is roughly 100px, which is enough for a 72px display.

**Verification links.** None were supplied, so the cards don't link anywhere. If the owner provides links, the title becomes a link in the existing `target="_blank" rel="noopener"` style.

Add one sentence on the certifications to `README.md`.

## 5. Services section

Add `<section class="section" id="services">` between Projects and Stack, following the existing section markup, with a nav link between Projects and Stack.

**Header:** label `// 03 — Services`, title "What I can do for you", description "The work I take on, for teams and freelance clients."

**Grid:** `.services-grid` has four columns, two at 1100px and one at 600px, matching `.projects-grid`.

**Cards:** `.service-card glow reveal` with an `<h3>`, a description, a divider and two chips.

| Title | Description | Chips |
| --- | --- | --- |
| Backend & API Development | Django and FastAPI backends with authentication, PostgreSQL and background jobs. | REST APIs, Background jobs |
| Machine Learning Solutions | Classification models, from cleaning the data to a deployed app. | Data cleaning, Model deployment |
| Full-Stack Web Apps | Complete web apps, with a React frontend on a Python backend. | Responsive UI, End-to-end builds |
| CRM Setup & Management | Zoho and Odoo set up around how a business tracks its customers and sales. | Zoho, Odoo |

This copy is drawn from what the site already claims, and the owner can edit it.

**Renumbering.** Section labels after Services shift by one: Stack 04, Journey 05, Focus 06, Approach 07, GitHub 08, Contact 09.

**Reveal delays.** Add `.services-grid` to the existing `nth-child` delay rules, using the same steps as `.projects-grid`.

## Effects on existing behaviour

- **Ocean backdrop:** no changes needed. The GSAP path scales with page height, and the bubble triggers already select all of `main > section`. Lite mode watches `#contact`, which is unaffected.
- **No-JS and reduced motion:** the new cards use the existing `.reveal` handling, and no new animations are added.
- **Nav:** it gains an eighth link, so it must be checked for fit at widths just above the 760px mobile breakpoint.
- **`CLAUDE.md`:** replace "square corners and no shadows" with the new rounded look, and add Services to the note on project data in several places.

## Out of scope

- Centred section headings, the reference's fonts, and any colour changes
- Header and nav restyling
- A 2026 Journey entry, which the owner didn't ask for
- A `services` or `certs` terminal command
- A "Download CV" button or other reference features

## Verification

There are no automated tests. Serve the site locally and check:

- All listed boxes are rounded, with no square inner corners on images, the terminal bar or the hero photo
- The cursor glow follows the rounded edges
- The Stack grid has no gaps at 1280px, 900px and 600px
- The nav fits at about 780px
- Services and the certifications render in both full and `.ocean-lite` modes, and the kraken ending still plays
- The page reads correctly with JS disabled and with reduced motion
- Section numbers run 01 to 09 in order, and the terminal `stack` command shows the CRM line
