# Job Application Assistant for Amit Kuzi

## Role
This repo is a job application workspace. Claude acts as a career advisor and application assistant for Amit Kuzi, helping with:
1. **Job fit evaluation** - Assess job postings against your profile (skills, experience, behavioral traits)
2. **CV tailoring** - Adapt existing CV templates (LaTeX/moderncv) to target specific roles
3. **Cover letter writing** - Draft targeted cover letters using existing templates (LaTeX)
4. **Interview preparation** - Prepare answers, questions, and talking points for interviews
5. **Career strategy** - Advise on positioning and personal branding

## Candidate Profile

Full structured profile: `.claude/skills/job-application-assistant/01-candidate-profile.md` (authoritative for dates/titles). Behavioral profile: `02-behavioral-profile.md`.

### Identity
- **Name:** Amit Kuzi
- **Location:** Holon, Israel (Tel Aviv metro; up to ~40 min transit commute - Ra'anana/Herzliya/Kfar Saba down to Rehovot OK, Netanya too far) - this remains the range for local on-site/hybrid roles
- **Location scope (updated 2026-07-20):** also open to remote roles (any employer location) and relocation roles in the US, UK, Canada, and Western Europe
- **Languages:** Hebrew (native), English (fluent)
- **Status:** Concluding role at GreenRoad Technologies (2026); seeking next position
- **Contact:** amitkuzi@gmail.com | +972 (0)54 4543836 | linkedin.com/in/amitkuzi | github.com/amitkuzi | amitkuzi.co.il

### Education
- **B.Sc. Chemical Engineering** (1993-1998) - Technion, Israel Institute of Technology, Haifa
  - Topics: Process Engineering, Computer Applications

### Professional Experience (highlights; full history in 01-candidate-profile.md)
- **Principal .NET Developer & Software Architect** (2024-2026) - **GreenRoad Technologies** (Holon)
  - .NET 8/10 microservices; AI-facilitated legacy migration (CoreWCF to REST on Docker/K8s)
  - Collision Detection real-time engine, zero-crash; ODI async-leak fixes, zero downtime
- **Developer, Architect, Manager, Consultant** (2020-2024) - **Amit Kuzi Consulting** (Tel Aviv District)
- **Senior Software Engineer** (2021-2022) - **Microsoft** (Herzliya) - Power Query Dataflow, Power BI/Power Apps
- **Hands-on Team Leader** (2022-2023) - **Cynet** - legacy .NET Framework to .NET 6 migration
- **VP R&D / Hands-On R&D Manager** (2017-2020) - **Tzunami** (Herzliya) - ~30-person group incl. 25 offshore (Nepal)
- **Tools Team Leader** (2015-2017) - **Amimon** (Ra'anana)
- Earlier roles 1997-2015: CodeValue, Cellebrite, Netformx, E4D/log-on, Check Point, and more

### Technical Skills
- **Primary:** C#, .NET (Framework through .NET 10), backend, microservices, RESTful APIs, software architecture, distributed systems, legacy modernization & migration, Docker, Kubernetes
- **Secondary:** ETL/data pipelines, cloud storage integrations (GDrive/OneDrive/Box), WPF/desktop (legacy depth), Angular (course-level)
- **Domain:** AI-assisted engineering (Claude Code, MCP, prompt engineering, agentic workflows), telematics/real-time systems, data migration products, cybersecurity (Cynet)
- **Software:** Visual Studio, Git, Docker/K8s toolchain, Power BI/Power Query

### Certifications
- C# 7 First Look; C#: Design Patterns; Learning ASP.NET Core MVC; Angular Essential Training

### Publications
- None

### Awards
- None recorded

### Behavioral Profile (full detail in 02-behavioral-profile.md)
- **Ownership-driven** - takes ownership, sees things through; hands-on when needed, hands-off when things work
- **Systems thinker** - root causes, whole-picture design that survives real-world pressure
- **Mentor-leader** - mentors without ego; high bar for professionalism
- **Anti-hype pragmatist** - simple solutions that last; skeptical of hype unless it solves a real problem
- **Strengths:** direct honest communication, legacy-modernization leadership, global team building
- **Growth areas:** low tolerance for prolonged ambiguity and repeated mistakes (frame as structure-creator)
- **Thrives in:** trust, clarity, psychological safety; clear goals; learning-not-blame culture
- **Hands-on scope (2026-07-20 clarification):** hands-on coding matters for Tech Lead/IC-track roles and any .NET-labeled role. Architect and Manager-track roles do NOT need to be hands-on - pure design/strategy (Architect) or pure people-management (Manager) is fine at that seniority.

### What Excites You
- Legacy modernization at scale and AI-driven development (Claude Code workflows)
- Building/mentoring confident teams; stable systems under real load; meaningful problems with real impact

### Target Sectors
- **Target roles:** Principal Developer / Tech Lead, Software Architect, Engineering Manager, Technology Consultant
- **Big tech / large enterprise (Israel R&D centers):** Google, Microsoft, NVIDIA, Amazon/AWS, Apple, Meta, Intel, and similar
- **Israeli product companies & scale-ups:** telematics, SaaS, cybersecurity, data platforms
- **Consulting firms:** CodeValue-style architecture/consulting practices

### Deal-breakers
- Pure maintenance roles with no development
- "Move fast and break things" culture as a virtue; vague goals / constantly shifting expectations

(Removed 2026-07-20, no longer deal-breakers: relocation - see Location scope above; pure people-management/pure architecture with no hands-on coding - see Hands-on scope above, still applies to Tech Lead/IC and .NET roles.)

## Repo Structure
- `cv/` - LaTeX CV variants (moderncv template, banking style)
- `cover_letters/` - LaTeX cover letters (custom cover.cls template)
- `.claude/skills/` - AI skill definitions for the application workflow
- `.agents/skills/` - Job search CLI tools

## Workflow for New Job Applications
1. User provides a job posting (URL or text)
2. **Always evaluate fit first**: skills match, experience match, behavioral/culture match. Present this assessment to the user before proceeding.
3. If good fit: create targeted CV (`cv/main_<company>.tex`) and cover letter (`cover_letters/cover_<company>_<role>.tex`)
4. **Verify both documents** (see Verification Checklist below)
5. Prepare interview talking points based on the role requirements and your strengths

**Important:** When mentioning agentic coding or AI tooling in CVs/cover letters, explicitly reference **Claude Code** by name.

## Verification Checklist
After creating or updating a CV or cover letter, re-read the generated file and verify **all** of the following before presenting to the user. Report the results as a pass/fail checklist.

### Factual accuracy
- [ ] All claims match actual profile (CLAUDE.md / candidate profile) - no fabricated skills, experience, or achievements
- [ ] Job titles, dates, company names, and locations are correct
- [ ] Contact details are correct
- [ ] All company-specific claims (partnerships, products, technology, expansions) have been independently verified via WebFetch/WebSearch - do not trust reviewer agent research without verification

### Targeting
- [ ] Profile statement / opening paragraph is tailored to the specific role (not generic)
- [ ] Skills and experience bullets are reframed to match the job requirements
- [ ] Key job requirements are addressed (with gaps acknowledged where relevant)
- [ ] Nice-to-have requirements are highlighted where there is a match

### Consistency
- [ ] CV follows the standard 2-page moderncv/banking format
- [ ] Cover letter uses cover.cls template and established structure
- [ ] Tone is consistent across CV and cover letter
- [ ] No contradictions between CV and cover letter content

### Quality
- [ ] No LaTeX syntax errors (balanced braces, correct commands)
- [ ] No spelling or grammar errors
- [ ] Agentic coding / AI tooling references mention **Claude Code** by name
- [ ] Cover letter is addressed to the correct person (or "Dear Hiring Manager" if unknown)
- [ ] Cover letter fits approximately one page

### Compiled PDF verification (MANDATORY - never skip)
Both documents MUST be compiled and visually inspected via the Read tool on the PDF output. "Looks fine in the .tex" is not acceptable - LaTeX page-break decisions are unpredictable. Iterate until these all pass:
- [ ] CV compiled with **lualatex** (pdflatex often fails on modern MiKTeX with fontawesome5 font-expansion errors). Cover letter compiled with **xelatex** (cover.cls requires fontspec).
- [ ] **CV is exactly 2 pages** - not 1, not 3
- [ ] **No orphaned `\cventry` titles** - a job/education title must never sit at the bottom of a page with its bullets spilling to the next page. Use `\needspace{5\baselineskip}` before each `\cventry` to prevent this, and `\enlargethispage{2-3\baselineskip}` to rescue a trailing section that just barely spills
- [ ] **Cover letter is exactly 1 page** - signature block must fit with the body, never overflow
- [ ] **Cover letter bullet font matches body font** - `\lettercontent{}` must not wrap `\begin{itemize}...\end{itemize}` (the command's trailing `\\` errors on `\end{itemize}`, and moving itemize outside loses the Raleway font). Standard pattern: close `\lettercontent{}`, then wrap the list in `{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont \begin{itemize}...\end{itemize}\par}`

### ATS & keyword verification (CV)
ATS parsers read the PDF's embedded text layer, not the rendered page. Extract it with `pdftotext -layout` and verify what a parser sees. `pdftotext` (poppler) is optional - if missing, skip the parseability items with a warning and check keyword coverage from the visual PDF read instead.
- [ ] CV text layer extracts cleanly - no `(cid:*)` markers, `�` replacement characters, or text visible in the PDF but absent from the extraction
- [ ] Email and phone appear as **literal text** in the extraction (icon-glyph noise like `MOBILE-ALT`/`Envelope` is harmless, but a contact detail carried only by an icon or hyperlink is invisible to ATS)
- [ ] Reading order of the extracted text matches the visual order (single-column stock template is safe; multi-column custom templates are where this breaks)
- [ ] Posting keywords covered or honestly absent - synonym-only matches tightened to the posting's exact term where truthfully applicable, keywords the profile genuinely supports added to experience bullets, genuine gaps left visible and **never stuffed**
