# Rounded Cards, Services and Certifications Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restyle the site's boxes to match the reference portfolio, and add a Services section, a CRM skill group and two Cisco certification cards.

**Architecture:** A static site with no build step: all markup and JS are in `index.html`, all styles in `style.css`. One shared block of radius rules sets the new look, and each new section follows the existing section markup. The design is in `docs/superpowers/specs/2026-10-31-cards-services-certifications-design.md`.

**Tech Stack:** Plain HTML, CSS and vanilla JS. Pillow is used once, to crop the badge images.

## Global Constraints

- Keep the slate and emerald colours, the fonts and the ocean backdrop. Copy only box shape and inner layout from the reference.
- `*` resets `border-radius` to 0 on `style.css:36`, so every rounded element needs an explicit radius.
- `.glow` boxes must not use `overflow: hidden`, because it clips the cursor ring.
- No shadows, no `backdrop-filter` on cards, and no new animations.
- New cards use the existing `.reveal` and `.glow` classes.
- There is no test suite. Each task is verified with `grep` checks and a browser, using `python3 -m http.server 8000` from the repo root.
- Commit messages must not include any Claude attribution or `Co-Authored-By` line. Do not push.
- Do not commit `docs/` or `CLAUDE.md`. `docs/` would be published with the site, and `CLAUDE.md` is gitignored.
- Find code by the quoted text. Line numbers refer to commit `7bd6721` and shift as tasks are applied.

---

### Task 1: Rounded box style

**Files:**
- Modify: `style.css`

**Interfaces:**
- Produces: tokens `--radius` and `--radius-sm`, plus the shared radius rules. These already include `.service-card`, `.cert-card`, `.focus-tags li`, `.service-tags li` and `.think-num`, which later tasks rely on.

- [ ] **Step 1: Add the tokens**

In `:root` (`style.css:26`), replace:

```css
    --header-h: 64px;
```

with:

```css
    --header-h: 64px;
    --radius: 14px;
    --radius-sm: 8px;
```

- [ ] **Step 2: Make the cursor glow follow the corners**

Replace (`style.css:82-85`):

```css
.glow::before,
.glow::after {
    content: ''; position: absolute; pointer-events: none;
}
```

with:

```css
.glow::before,
.glow::after {
    content: ''; position: absolute; pointer-events: none; border-radius: inherit;
}
```

- [ ] **Step 3: Add the shared radius rules**

Directly after `.glow > * { position: relative; z-index: 1; }` (`style.css:100`), add:

```css

/* ===== ROUNDED BOXES =====
   The reset on * zeroes every radius, so anything rounded is listed here. .glow boxes can't
   use overflow: hidden, so their edge-to-edge children carry their own corners. */
.project-card, .stack-group, .tl-card, .focus-card, .think-card, .about-stat,
.gh-stat, .gh-card, .repo-card, .terminal, .hero-photo, .service-card, .cert-card { border-radius: var(--radius); }

.chip-row li, .project-stack li, .stack-group li, .tl-tags li, .focus-tags li, .service-tags li,
.stack-group li::after, .btn, .project-link, .project-tag, .badge,
.contact-link, .terminal-toggle, .think-num { border-radius: var(--radius-sm); }

.level-legend li::before, .stack-group li::before, .lang-name::before,
.tl-item::before, .terminal-dot { border-radius: 50%; }

.project-img-wrapper, .terminal-bar { border-radius: calc(var(--radius) - 1px) calc(var(--radius) - 1px) 0 0; }
.hero-photo img, .hero-photo .hero-caustics { border-radius: calc(var(--radius) - 8px); }
```

- [ ] **Step 4: Extend the shared chip look to the new chip lists**

Replace (`style.css:525`):

```css
.chip-row li, .project-stack li, .stack-group li, .tl-tags li {
```

with:

```css
.chip-row li, .project-stack li, .stack-group li, .tl-tags li, .focus-tags li, .service-tags li {
```

- [ ] **Step 5: Check it in the browser**

Run `python3 -m http.server 8000` and open `http://localhost:8000`.

Expected: project, stack, journey, focus, approach, about, GitHub and terminal boxes are all rounded. Project images and the terminal bar have no square corners poking out. The hero photo and its caustics are rounded. On desktop, the cursor glow follows the curved edges.

- [ ] **Step 6: Commit**

```bash
git add style.css
git commit -m "Round the site's boxes, chips and buttons"
```

---

### Task 2: Inner layouts for Case Files, Focus, Journey and Approach

**Files:**
- Modify: `index.html` (Focus section)
- Modify: `style.css` (PROJECTS, JOURNEY, FOCUS and APPROACH sections)

**Interfaces:**
- Consumes: `.focus-tags li` and `.think-num` radius rules from Task 1.
- Produces: nothing later tasks depend on.

- [ ] **Step 1: Add the Case Files divider**

Replace (`style.css:576`):

```css
.project-stack { display: flex; flex-wrap: wrap; gap: 5px; margin: auto 0 14px; padding-top: 4px; }
```

with:

```css
.project-stack { display: flex; flex-wrap: wrap; gap: 5px; margin: auto 0 14px; padding-top: 12px; border-top: 1px solid var(--border); }
```

- [ ] **Step 2: Add the Journey divider**

Replace (`style.css:646`):

```css
.tl-tags { display: flex; flex-wrap: wrap; gap: 6px; }
```

with:

```css
.tl-tags { display: flex; flex-wrap: wrap; gap: 6px; padding-top: 12px; border-top: 1px solid var(--border); }
```

- [ ] **Step 3: Turn the Focus detail line into chips (CSS)**

Replace (`style.css:657-661`):

```css
.focus-card > p:not(.focus-detail) { font-size: 0.88rem; color: var(--text-secondary); }
.focus-detail {
    margin-top: auto; padding-top: 12px; border-top: 1px solid var(--border);
    font-family: var(--mono); font-size: 0.7rem; color: var(--text-muted);
}
```

with:

```css
.focus-card > p { font-size: 0.88rem; color: var(--text-secondary); }
.focus-tags { display: flex; flex-wrap: wrap; gap: 6px; margin-top: auto; padding-top: 12px; border-top: 1px solid var(--border); }
.focus-tags li { font-size: 0.68rem; padding: 2px 8px; color: var(--text-muted); }
```

- [ ] **Step 4: Turn the Focus detail line into chips (HTML)**

In `index.html`, replace each of the six lines as follows.

`<p class="focus-detail">TensorFlow · PyTorch · LLM integration</p>` becomes:

```html
                        <ul class="focus-tags"><li>TensorFlow</li><li>PyTorch</li><li>LLM integration</li></ul>
```

`<p class="focus-detail">scikit-learn · pandas · Model deployment</p>` becomes:

```html
                        <ul class="focus-tags"><li>scikit-learn</li><li>pandas</li><li>Model deployment</li></ul>
```

`<p class="focus-detail">Django REST · FastAPI · PostgreSQL · Celery</p>` becomes:

```html
                        <ul class="focus-tags"><li>Django REST</li><li>FastAPI</li><li>PostgreSQL</li><li>Celery</li></ul>
```

`<p class="focus-detail">React · Next.js · TypeScript · Tailwind</p>` becomes:

```html
                        <ul class="focus-tags"><li>React</li><li>Next.js</li><li>TypeScript</li><li>Tailwind</li></ul>
```

`<p class="focus-detail">Distributed systems · API design · Caching</p>` becomes:

```html
                        <ul class="focus-tags"><li>Distributed systems</li><li>API design</li><li>Caching</li></ul>
```

`<p class="focus-detail">Docker · Kubernetes · CI/CD</p>` becomes:

```html
                        <ul class="focus-tags"><li>Docker</li><li>Kubernetes</li><li>CI/CD</li></ul>
```

- [ ] **Step 5: Turn the Approach number into a tile**

Replace (`style.css:677`):

```css
.think-num { display: block; font-family: var(--mono); font-size: 0.85rem; color: var(--accent); margin-bottom: 12px; }
```

with:

```css
.think-num {
    display: inline-flex; align-items: center; justify-content: center;
    width: 36px; height: 36px; margin-bottom: 14px;
    background: var(--bg-elevated); border: 1px solid var(--border-light);
    font-family: var(--mono); font-size: 0.8rem; color: var(--accent);
}
```

- [ ] **Step 6: Verify**

Run: `grep -c 'focus-detail' index.html style.css`
Expected: `index.html:0` and `style.css:0`

Run: `grep -c 'class="focus-tags"' index.html`
Expected: `6`

In the browser: Case Files and Journey boxes show a thin divider above their chips, Focus boxes show chips under a divider, and Approach boxes show "01", "02" and "03" in small rounded tiles.

- [ ] **Step 7: Commit**

```bash
git add index.html style.css
git commit -m "Give card interiors dividers, chips and numbered tiles"
```

---

### Task 3: Skills layout and CRM group

**Files:**
- Modify: `index.html` (the `.stack-grid` block and the terminal `stack` command)
- Modify: `style.css` (STACK and RESPONSIVE sections)
- Modify: `README.md`

**Interfaces:**
- Consumes: `.stack-group li` radius rules from Task 1.
- Produces: `.stack-wide`, and a `</div>` closing `.stack-grid` that Task 4 inserts after.

- [ ] **Step 1: Replace the Stack grid markup**

In `index.html`, replace everything from `<div class="stack-grid">` through its closing `</div>` (`index.html:298-368`) with this. It moves each note above its chips, adds `.stack-wide` to Tools & ops, and adds the CRM group:

```html
                <div class="stack-grid">
                    <div class="stack-group stack-primary glow reveal">
                        <h3>Backend</h3>
                        <p class="stack-note">Where I spend most of my time.</p>
                        <ul>
                            <li data-level="proficient">Python<span class="sr-only">, proficient</span></li>
                            <li data-level="confident">Django<span class="sr-only">, confident</span></li>
                            <li data-level="proficient">Django REST<span class="sr-only">, proficient</span></li>
                            <li data-level="growing">FastAPI<span class="sr-only">, growing</span></li>
                            <li data-level="growing">Redis<span class="sr-only">, growing</span></li>
                            <li data-level="growing">Celery<span class="sr-only">, growing</span></li>
                        </ul>
                    </div>
                    <div class="stack-group glow reveal">
                        <h3>Databases</h3>
                        <p class="stack-note">PostgreSQL unless there's a reason not to.</p>
                        <ul>
                            <li data-level="proficient">PostgreSQL<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">SQL<span class="sr-only">, proficient</span></li>
                            <li data-level="confident">SQLite<span class="sr-only">, confident</span></li>
                            <li data-level="growing">MongoDB<span class="sr-only">, growing</span></li>
                        </ul>
                    </div>
                    <div class="stack-group glow reveal">
                        <h3>Frontend</h3>
                        <p class="stack-note">I debug more than I write.</p>
                        <ul>
                            <li data-level="proficient">JavaScript<span class="sr-only">, proficient</span></li>
                            <li data-level="confident">HTML &amp; CSS<span class="sr-only">, confident</span></li>
                            <li data-level="growing">TypeScript<span class="sr-only">, growing</span></li>
                            <li data-level="growing">React<span class="sr-only">, growing</span></li>
                            <li data-level="proficient">jQuery<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">Bootstrap<span class="sr-only">, proficient</span></li>
                        </ul>
                    </div>
                    <div class="stack-group glow reveal">
                        <h3>Data &amp; ML</h3>
                        <p class="stack-note">Pandas is my flatmate.</p>
                        <ul>
                            <li data-level="proficient">scikit-learn<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">pandas<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">numpy<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">matplotlib<span class="sr-only">, proficient</span></li>
                            <li data-level="proficient">Streamlit<span class="sr-only">, proficient</span></li>
                            <li data-level="learning">TensorFlow<span class="sr-only">, learning</span></li>
                            <li data-level="learning">PyTorch<span class="sr-only">, learning</span></li>
                        </ul>
                    </div>
                    <div class="stack-group glow reveal">
                        <h3>Desktop, mobile &amp; hardware</h3>
                        <p class="stack-note">I make LEDs blink.</p>
                        <ul>
                            <li data-level="proficient">PyQt5<span class="sr-only">, proficient</span></li>
                            <li data-level="learning">Kotlin<span class="sr-only">, learning</span></li>
                            <li data-level="growing">Swing<span class="sr-only">, growing</span></li>
                            <li data-level="learning">Jetpack Compose<span class="sr-only">, learning</span></li>
                            <li data-level="growing">Arduino<span class="sr-only">, growing</span></li>
                        </ul>
                    </div>
                    <div class="stack-group stack-wide glow reveal">
                        <h3>Tools &amp; ops</h3>
                        <p class="stack-note">Containers and CI. Orchestration is next.</p>
                        <ul>
                            <li data-level="confident">Git &amp; GitHub<span class="sr-only">, confident</span></li>
                            <li data-level="proficient">Linux<span class="sr-only">, proficient</span></li>
                            <li data-level="growing">Docker<span class="sr-only">, growing</span></li>
                            <li data-level="growing">GitHub Actions<span class="sr-only">, growing</span></li>
                            <li data-level="learning">Kubernetes<span class="sr-only">, learning</span></li>
                        </ul>
                    </div>
                    <div class="stack-group glow reveal">
                        <h3>CRM &amp; business tools</h3>
                        <p class="stack-note">Customers, pipelines and follow-ups.</p>
                        <ul>
                            <li data-level="growing">Zoho<span class="sr-only">, growing</span></li>
                            <li data-level="growing">Odoo<span class="sr-only">, growing</span></li>
                        </ul>
                    </div>
                </div>
```

- [ ] **Step 2: Update the Stack card CSS**

Replace (`style.css:603-605`):

```css
.stack-group { background: var(--bg-card); border: 1px solid var(--border); padding: 22px; }
.stack-group h3 { font-family: var(--mono); font-size: 0.95rem; font-weight: 600; color: var(--text); margin-bottom: 16px; }
.stack-group ul { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 16px; }
```

with:

```css
.stack-group { background: var(--bg-card); border: 1px solid var(--border); padding: 22px; }
.stack-primary, .stack-wide { grid-column: span 2; }
.stack-group h3 { font-family: var(--mono); font-size: 0.95rem; font-weight: 600; color: var(--text); margin-bottom: 8px; }
.stack-group ul { display: flex; flex-wrap: wrap; gap: 8px; padding-top: 14px; border-top: 1px solid var(--border); }
```

Then replace (`style.css:625`):

```css
.stack-note { font-family: var(--mono); font-size: 0.78rem; color: var(--text-muted); }
```

with:

```css
.stack-note { font-family: var(--mono); font-size: 0.78rem; color: var(--text-muted); margin-bottom: 14px; }
```

- [ ] **Step 3: Reset the spans on smaller screens**

Replace (`style.css:819`):

```css
    .stack-grid, .focus-grid { grid-template-columns: repeat(2, 1fr); }
```

with:

```css
    .stack-grid, .focus-grid { grid-template-columns: repeat(2, 1fr); }
    .stack-wide { grid-column: auto; }
```

Replace (`style.css:836`):

```css
    .projects-grid, .stack-grid, .focus-grid, .repo-grid { grid-template-columns: 1fr; }
```

with:

```css
    .projects-grid, .stack-grid, .focus-grid, .repo-grid { grid-template-columns: 1fr; }
    .stack-primary { grid-column: auto; }
```

- [ ] **Step 4: Update the terminal `stack` command**

In `index.html`, replace (`index.html:1036`):

```js
                stack: () => ['out', 'Backend:   Python, Django, FastAPI, Redis, Celery\nData:      PostgreSQL, SQLite, MongoDB\nFrontend:  JavaScript, TypeScript, React, jQuery\nML:        scikit-learn, pandas, numpy\nOther:     PyQt5, Kotlin, Docker, Arduino'],
```

with:

```js
                stack: () => ['out', 'Backend:   Python, Django, FastAPI, Redis, Celery\nData:      PostgreSQL, SQLite, MongoDB\nFrontend:  JavaScript, TypeScript, React, jQuery\nML:        scikit-learn, pandas, numpy\nCRM:       Zoho, Odoo\nOther:     PyQt5, Kotlin, Docker, Arduino'],
```

- [ ] **Step 5: Update the README stack line**

In `README.md`, replace line 10:

```markdown
**Stack:** Python (Django, FastAPI) · JavaScript · PostgreSQL · Redis/Celery · Docker
```

with:

```markdown
**Stack:** Python (Django, FastAPI) · JavaScript · PostgreSQL · Redis/Celery · Docker · Zoho · Odoo
```

- [ ] **Step 6: Verify**

Run: `grep -c 'class="stack-group' index.html`
Expected: `7`

In the browser, using the device toolbar:

- At 1280px: three full rows, with Backend and Tools & ops double width and no gaps.
- At 800px: Backend fills the first row, then three rows of two, with no gaps.
- At 500px: one column with no horizontal scrolling.
- Hovering a chip still shows its level label.
- Typing `stack` in the contact terminal prints the `CRM:` line.

- [ ] **Step 7: Commit**

```bash
git add index.html style.css README.md
git commit -m "Move skill notes above chips and add a CRM group"
```

---

### Task 4: Certifications

**Files:**
- Create: `Assets/images/cert-data-science.png`
- Create: `Assets/images/cert-cybersecurity.png`
- Modify: `index.html` (after `.stack-grid`)
- Modify: `style.css` (STACK and RESPONSIVE sections)
- Modify: `README.md`

**Interfaces:**
- Consumes: the `.cert-card` radius rule from Task 1, and the `.stack-grid` closing tag from Task 3.
- Produces: `.cert-grid`, which Task 5 does not touch.

- [ ] **Step 1: Create the badge images**

If the owner has supplied original badge files, copy them to the two paths above and skip the crop. Otherwise crop them from the owner's screenshot:

```bash
python3 -I -c "
from PIL import Image
src = Image.open('/tmp/claude-1000/-home-muhammadibrahim-Projects-Baboosh617/5a2e5d8c-d0ab-4427-ac1d-aab788366e21/images/1.png').convert('RGB')
src.crop((115, 52, 215, 152)).save('Assets/images/cert-data-science.png')
src.crop((416, 52, 516, 152)).save('Assets/images/cert-cybersecurity.png')
"
```

Open both files and confirm the crop is tight on the badge, with the blue one saved as data science and the green one as cybersecurity. Adjust the crop boxes by a pixel or two if needed. The screenshot's white background may remain in the badge corners, and the `border-radius` in Step 3 clips it.

- [ ] **Step 2: Add the certifications markup**

In `index.html`, directly after the `</div>` closing `.stack-grid`, add:

```html

                <div class="certs">
                    <p class="mini-label reveal">Certifications</p>
                    <div class="cert-grid">
                        <article class="cert-card glow reveal">
                            <img src="Assets/images/cert-data-science.png" alt="Cisco Networking Academy badge for Data Science Essentials with Python" width="72" height="72" loading="lazy">
                            <div>
                                <h3>Data Science Essentials with Python</h3>
                                <p class="cert-issuer">Cisco Networking Academy</p>
                                <p class="cert-date">Issued <time datetime="2026-10-28">Oct 28, 2026</time></p>
                            </div>
                        </article>
                        <article class="cert-card glow reveal">
                            <img src="Assets/images/cert-cybersecurity.png" alt="Cisco Networking Academy badge for Introduction to Cybersecurity" width="72" height="72" loading="lazy">
                            <div>
                                <h3>Introduction to Cybersecurity</h3>
                                <p class="cert-issuer">Cisco Networking Academy</p>
                                <p class="cert-date">Issued <time datetime="2026-10-28">Oct 28, 2026</time></p>
                            </div>
                        </article>
                    </div>
                </div>
```

- [ ] **Step 3: Add the certifications CSS**

In `style.css`, directly before `/* ===== JOURNEY ===== */`, add:

```css
/* certifications sit under the skills grid */
.certs { margin-top: 36px; }
.cert-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
.cert-card {
    display: flex; align-items: center; gap: 18px; padding: 20px;
    background: var(--bg-card); border: 1px solid var(--border);
}
.cert-card img { flex-shrink: 0; width: 72px; height: 72px; border-radius: 10px; }
.cert-card h3 { font-size: 1rem; font-weight: 700; line-height: 1.3; margin-bottom: 4px; }
.cert-issuer { font-size: 0.85rem; color: var(--text-secondary); }
.cert-date { font-family: var(--mono); font-size: 0.72rem; color: var(--text-muted); margin-top: 4px; }

```

Then replace (the line changed in Task 3):

```css
    .projects-grid, .stack-grid, .focus-grid, .repo-grid { grid-template-columns: 1fr; }
```

with:

```css
    .projects-grid, .stack-grid, .focus-grid, .repo-grid, .cert-grid { grid-template-columns: 1fr; }
```

- [ ] **Step 4: Add the certifications to the README**

In `README.md`, directly after the `**Stack:**` line, add a blank line and:

```markdown
**Certifications:** Data Science Essentials with Python and Introduction to Cybersecurity (Cisco Networking Academy, 2026)
```

- [ ] **Step 5: Verify**

Run: `grep -c 'class="cert-card' index.html`
Expected: `2`

Run: `ls Assets/images/cert-*`
Expected: both PNG files are listed.

In the browser: two cards appear under the skills grid, side by side at 1280px and stacked at 500px. Badges are sharp at 72px with no white corners, and the text matches the screenshot.

- [ ] **Step 6: Commit**

```bash
git add Assets/images/cert-data-science.png Assets/images/cert-cybersecurity.png index.html style.css README.md
git commit -m "Add Cisco certification cards under the skills grid"
```

---

### Task 5: Services section, nav link and renumbering

**Files:**
- Modify: `index.html` (nav, new section, section labels)
- Modify: `style.css` (new section, reveal delays, RESPONSIVE section)

**Interfaces:**
- Consumes: `.service-card` and `.service-tags li` radius and chip rules from Task 1.
- Produces: `#services`, numbered `// 03`.

- [ ] **Step 1: Add the nav link**

In `index.html`, replace:

```html
                    <li><a href="#projects">Projects</a></li>
```

with:

```html
                    <li><a href="#projects">Projects</a></li>
                    <li><a href="#services">Services</a></li>
```

- [ ] **Step 2: Add the section**

In `index.html`, directly before `<!-- Stack -->`, add:

```html
        <!-- Services -->
        <section class="section" id="services" aria-labelledby="services-heading">
            <div class="container">
                <div class="section-head reveal">
                    <p class="section-label">// 03 — Services</p>
                    <h2 class="section-title" id="services-heading">What I can do for you</h2>
                    <p class="section-desc">The work I take on, for teams and freelance clients.</p>
                </div>
                <div class="services-grid">
                    <article class="service-card glow reveal">
                        <h3>Backend &amp; API Development</h3>
                        <p>Django and FastAPI backends with authentication, PostgreSQL and background jobs.</p>
                        <ul class="service-tags"><li>REST APIs</li><li>Background jobs</li></ul>
                    </article>
                    <article class="service-card glow reveal">
                        <h3>Machine Learning Solutions</h3>
                        <p>Classification models, from cleaning the data to a deployed app.</p>
                        <ul class="service-tags"><li>Data cleaning</li><li>Model deployment</li></ul>
                    </article>
                    <article class="service-card glow reveal">
                        <h3>Full-Stack Web Apps</h3>
                        <p>Complete web apps, with a React frontend on a Python backend.</p>
                        <ul class="service-tags"><li>Responsive UI</li><li>End-to-end builds</li></ul>
                    </article>
                    <article class="service-card glow reveal">
                        <h3>CRM Setup &amp; Management</h3>
                        <p>Zoho and Odoo set up around how a business tracks its customers and sales.</p>
                        <ul class="service-tags"><li>Zoho</li><li>Odoo</li></ul>
                    </article>
                </div>
            </div>
        </section>

```

- [ ] **Step 3: Renumber the later sections**

In `index.html`, make these replacements:

| Find | Replace with |
| --- | --- |
| `// 03 — Stack` | `// 04 — Stack` |
| `// 04 — Journey` | `// 05 — Journey` |
| `// 05 — Focus` | `// 06 — Focus` |
| `// 06 — Approach` | `// 07 — Approach` |
| `// 07 — GitHub` | `// 08 — GitHub` |
| `// 08 — Contact` | `// 09 — Contact` |

Apply them from the bottom row up, so no label is changed twice.

- [ ] **Step 4: Add the Services CSS**

In `style.css`, directly before `/* ===== STACK + SKILL LEVELS ===== */`, add:

```css
/* ===== SERVICES ===== */
.services-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; }
.service-card {
    display: flex; flex-direction: column; gap: 12px; padding: 22px;
    background: var(--bg-card); border: 1px solid var(--border);
}
.service-card h3 { font-size: 1.05rem; font-weight: 700; line-height: 1.3; }
.service-card > p { font-size: 0.88rem; color: var(--text-secondary); }
.service-tags { display: grid; gap: 8px; margin-top: auto; padding-top: 14px; border-top: 1px solid var(--border); }
.service-tags li { padding: 8px 12px; }

```

- [ ] **Step 5: Add the reveal delays**

Replace (`style.css:801-803`):

```css
.projects-grid .reveal:nth-child(4n+2), .stack-grid .reveal:nth-child(3n+2), .focus-grid .reveal:nth-child(3n+2), .think-grid .reveal:nth-child(2) { transition-delay: 0.08s; }
.projects-grid .reveal:nth-child(4n+3), .stack-grid .reveal:nth-child(3n), .focus-grid .reveal:nth-child(3n), .think-grid .reveal:nth-child(3) { transition-delay: 0.16s; }
.projects-grid .reveal:nth-child(4n) { transition-delay: 0.24s; }
```

with:

```css
.projects-grid .reveal:nth-child(4n+2), .services-grid .reveal:nth-child(4n+2), .stack-grid .reveal:nth-child(3n+2), .focus-grid .reveal:nth-child(3n+2), .think-grid .reveal:nth-child(2) { transition-delay: 0.08s; }
.projects-grid .reveal:nth-child(4n+3), .services-grid .reveal:nth-child(4n+3), .stack-grid .reveal:nth-child(3n), .focus-grid .reveal:nth-child(3n), .think-grid .reveal:nth-child(3) { transition-delay: 0.16s; }
.projects-grid .reveal:nth-child(4n), .services-grid .reveal:nth-child(4n) { transition-delay: 0.24s; }
```

- [ ] **Step 6: Update the responsive rules**

Replace (`style.css:812`):

```css
    .projects-grid { grid-template-columns: repeat(2, 1fr); }
```

with:

```css
    .projects-grid, .services-grid { grid-template-columns: repeat(2, 1fr); }
```

In the `@media (max-width: 900px)` block, directly after `.hero-grid { gap: 40px; }`, add this so the longer nav still fits. The 760px block comes later and still overrides it for the mobile menu:

```css
    .nav-links { gap: 18px; }
```

Replace (the line changed in Task 4):

```css
    .projects-grid, .stack-grid, .focus-grid, .repo-grid, .cert-grid { grid-template-columns: 1fr; }
```

with:

```css
    .projects-grid, .services-grid, .stack-grid, .focus-grid, .repo-grid, .cert-grid { grid-template-columns: 1fr; }
```

- [ ] **Step 7: Verify**

Run: `grep -o '// 0[0-9] — [A-Za-z]*' index.html`
Expected, in this order: `01 About`, `02 Projects`, `03 Services`, `04 Stack`, `05 Journey`, `06 Focus`, `07 Approach`, `08 GitHub`, `09 Contact`, each in the `// NN — Name` format.

In the browser:

- The Services nav link scrolls to the new section.
- The grid shows four cards at 1280px, two at 1000px and one at 500px.
- At 780px, the nav stays on one line and doesn't overflow.

- [ ] **Step 8: Commit**

```bash
git add index.html style.css
git commit -m "Add Services section and renumber later sections"
```

---

### Task 6: Full-page check and CLAUDE.md update

**Files:**
- Modify: `CLAUDE.md` (gitignored, so not committed)

**Interfaces:**
- Consumes: everything from Tasks 1-5.
- Produces: nothing.

- [ ] **Step 1: Update `CLAUDE.md`**

Replace:

```markdown
- **Design tokens** are CSS variables in `:root` (slate and emerald palette). The look uses square corners and no shadows.
```

with:

```markdown
- **Design tokens** are CSS variables in `:root` (slate and emerald palette). The look uses rounded boxes and no shadows. The `*` reset zeroes every radius, so new rounded elements must be added to the `ROUNDED BOXES` rules in `style.css`, and edge-to-edge children of `.glow` boxes need their own corners.
```

Then, at the end of the "Project data lives in several places" paragraph, add:

```markdown
 Service cards in `#services` restate skills from `#stack`, so keep the two consistent.
```

- [ ] **Step 2: Check both ocean modes**

With the server running, scroll the full page at 1280px in a desktop browser.

Expected: the fish swims the whole page, the kraken rises at the bottom, and the fish turns to a skeleton.

Then enable a touch device in the device toolbar and reload, or confirm `document.documentElement.classList.contains('ocean-lite')` is `true` in the console.

Expected: the flat fish swims, and the kraken and bones appear when Contact scrolls into view.

- [ ] **Step 3: Check no-JS and reduced motion**

Disable JavaScript in dev tools and reload.

Expected: all sections, including Services and the certifications, are visible.

Turn JS back on and emulate `prefers-reduced-motion: reduce`, then reload.

Expected: all content is visible, with no fish, kraken or caustics.

- [ ] **Step 4: Check for console errors and a clean tree**

Reload with the console open.

Expected: no new errors. The GitHub API warning may appear if rate limited, and it isn't caused by this work.

Run: `git status --short`
Expected: only `docs/` and any previously uncommitted `.gitignore` appear as untracked.
