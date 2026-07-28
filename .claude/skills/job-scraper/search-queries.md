# Search Queries for Job Scraper

<!-- Personalized by /setup for Amit Kuzi (2026-07). Target roles: Principal Developer / Tech Lead, Software Architect, Engineering Manager, Technology Consultant. -->

## Search Sites

Two channels (user decision 2026-07-14 - amends the earlier "LinkedIn only" decision):

1. **linkedin.com/jobs** - via the `linkedin-search` CLI skill (location filter: Israel / Tel Aviv metro, plus Remote and relocation regions - see Location Scope below)
2. **Big-tech & large-enterprise career pages** - direct checks via WebFetch/WebSearch (see section below). No CLI skill exists for these; use `site:` queries or fetch the filtered career-page URLs.

Do NOT add other generic job boards without the user's approval; this setup is intentionally limited to LinkedIn + direct company career pages. See "Additional Sources" below for options if broader coverage is wanted - onboard any of them properly via `/add-portal` rather than ad-hoc WebSearch scraping.

## Query Categories

Queries are grouped by priority. Combine each with the location filter where the site supports it.

### Priority 1: Principal Developer / Tech Lead

Strongest and most desired direction - hands-on technical leadership.

```
linkedin-search: -q "Principal Software Engineer .NET" -l "Tel Aviv, Israel"
linkedin-search: -q "Principal Developer" -l "Tel Aviv, Israel"
linkedin-search: -q "Tech Lead .NET" -l "Tel Aviv, Israel"
linkedin-search: -q "Technical Lead backend" -l "Tel Aviv, Israel"
linkedin-search: -q "Staff Software Engineer backend" -l "Tel Aviv, Israel"
```

### Priority 2: Software Architect

Domain-expertise direction - system design and modernization.

```
linkedin-search: -q "Software Architect .NET" -l "Tel Aviv, Israel"
linkedin-search: -q "Software Architect microservices" -l "Tel Aviv, Israel"
linkedin-search: -q "Solutions Architect backend" -l "Tel Aviv, Israel"
linkedin-search: -q "System Architect distributed systems" -l "Tel Aviv, Israel"
```

### Priority 3: Engineering Manager

People + technology leadership. No hands-on coding requirement at this level (updated 2026-07-20) - pure people-management is fine.

```
linkedin-search: -q "Engineering Manager hands-on" -l "Tel Aviv, Israel"
linkedin-search: -q "R&D Team Leader software" -l "Tel Aviv, Israel"
linkedin-search: -q "Engineering Group Leader" -l "Tel Aviv, Israel"
linkedin-search: -q "Head of Engineering" -l "Tel Aviv, Israel"
```

### Priority 4: Consulting & Wider Net

Consulting positions leveraging the 2020-2024 independent practice, plus broader senior-backend roles.

```
linkedin-search: -q "Technology Consultant software" -l "Tel Aviv, Israel"
linkedin-search: -q "Technical Consultant .NET" -l "Tel Aviv, Israel"
linkedin-search: -q "Software Consultant architect" -l "Tel Aviv, Israel"
linkedin-search: -q "Senior Backend Engineer C# .NET" -l "Tel Aviv, Israel"
linkedin-search: -q "legacy modernization .NET migration" -l "Israel"
linkedin-search: -q "AI-assisted development engineer" -l "Israel"
```

### Priority 5: Remote & Relocation (added 2026-07-20)

Same role focus as Priorities 1-2 (Tech Lead/IC and Software Architect are the strongest matches), but widened beyond Israel. Run this category when the user asks for a broad/international sweep, not by default on every `/scrape` (keeps routine runs fast and local-first).

```
linkedin-search: -q "Software Architect .NET" -l "Remote"
linkedin-search: -q "Principal Software Engineer .NET" -l "Remote"
linkedin-search: -q "Software Architect microservices" -l "United States"
linkedin-search: -q "Software Architect microservices" -l "United Kingdom"
linkedin-search: -q "Software Architect microservices" -l "Canada"
linkedin-search: -q "Software Architect .NET" -l "Germany"
linkedin-search: -q "Software Architect .NET" -l "Netherlands"
```

Relocation region scope (user decision 2026-07-20): **US, UK, Canada, Western Europe only.** Do not surface relocation postings outside this set without checking with the user first (see Location Scope below - a FLAG, not an auto-include). Remote postings have no such restriction - any employer location is fine since there's no physical move.

### Distinctive skill terms (mix into any query)

`C#` · `.NET 8/10` · `microservices` · `Kubernetes` · `legacy migration` / `.NET modernization` · `distributed systems` · `AI-assisted development` / `Claude Code`

## Big-Tech & Large-Enterprise Career Pages (GAFAM+ / Israel R&D)

Check these directly during /scrape - many big-tech postings appear here before or instead of LinkedIn. All have significant Israel R&D centers. Career-site URLs drift; if a filtered URL 404s, WebSearch `site:<domain> <role> Israel` instead.

### Tier 1 (user-named)

| Company | Careers search (Israel) | Notes |
|---------|------------------------|-------|
| Google | https://www.google.com/about/careers/applications/jobs/results/?location=Israel | Tel Aviv + Haifa sites |
| Microsoft | https://jobs.careers.microsoft.com/global/en/search?lc=Israel | Herzliya (ILDC) - former employer, alumni advantage |
| NVIDIA | https://nvidia.wd5.myworkdayjobs.com/NVIDIAExternalCareerSite | Filter location: Israel. Yokneam is beyond commute; Tel Aviv site OK |
| Amazon / AWS | https://www.amazon.jobs/en/search?loc_query=Israel | Tel Aviv + Haifa (Annapurna) |
| Apple | https://jobs.apple.com/en-il/search?location=israel-ISR | Herzliya |
| Meta | https://www.metacareers.com/jobs (filter office: Tel Aviv, Israel) | Tel Aviv |

### Tier 2 (large enterprise, Israel R&D)

| Company | Careers search | Notes |
|---------|---------------|-------|
| Intel | https://jobs.intel.com (filter Israel) | Petah Tikva closest site; Haifa/Kiryat Gat far |
| IBM | https://www.ibm.com/careers/search (filter Israel) | Research + software labs |
| Oracle | https://careers.oracle.com/jobs (filter Israel) | |
| Salesforce | https://careers.salesforce.com/en/jobs/ (filter Israel) | Tel Aviv |
| SAP | https://jobs.sap.com/search/?locationsearch=Israel | Ra'anana |
| Cisco | https://jobs.cisco.com (filter Israel) | |
| Check Point | https://careers.checkpoint.com/ | Tel Aviv - former employer (2006) |

### How to query career pages

- WebSearch pattern: `site:jobs.careers.microsoft.com "Principal Software Engineer" Israel` (swap domain + role per company)
- Role terms to rotate: "Principal Software Engineer", "Principal Engineer", "Software Architect", "Engineering Manager", "Tech Lead", "Staff Engineer"
- Skill terms that map well at big tech: C#/.NET, distributed systems, microservices, high-scale backend
- To promote a company page into a proper CLI skill later, run `/add-portal` with its careers URL.

## Location Filter (updated 2026-07-20)

When evaluating results, classify the job location as one of:
- **Ideal (local):** Holon, Tel Aviv and immediate surroundings
- **Acceptable (local):** Ra'anana / Herzliya / Kfar Saba in the north down to Rehovot in the south
- **Borderline (local):** anything up to ~40 min by transit from Holon - flag for user judgment
- **Too far (local, non-remote):** Netanya and beyond, Haifa, Yokneam, Jerusalem, Beer Sheva - exclude unless the role is remote/hybrid-mostly
- **Remote:** PASS regardless of employer location - no physical move required
- **Relocation, US/UK/Canada/Western Europe:** PASS - approved region (user decision 2026-07-20)
- **Relocation, elsewhere:** FLAG for user judgment, do not auto-exclude but do not treat as pre-approved either

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Additional Sources (candidates, not yet wired in - added 2026-07-20)

Current coverage is LinkedIn (CLI) + direct big-tech career pages (WebSearch/WebFetch). Now that remote and US/UK/Canada/Western-Europe relocation are in scope, these are worth adding via `/add-portal` (which builds a proper CLI skill, same pattern as `linkedin-search`) rather than ad-hoc WebSearch:

| Source | Best for | Notes |
|--------|----------|-------|
| Indeed | Broad coverage, has both Israel and international/remote filters | Largest volume; more noise than LinkedIn, worth a targeted `/add-portal` |
| Wellfound (AngelList Talent) | Remote & startup/scale-up roles, often with salary transparency | Skews startup, matches your Israeli scale-up target sector too |
| We Work Remotely / RemoteOK | Remote-only roles | Useful now that remote is in scope; lower volume of Architect/Principal-level roles than LinkedIn |
| Otta | Curated tech roles, US/UK/Europe scale-ups | Good fit for the new relocation regions specifically |
| Built In | US-specific, city-based tech hubs (NYC, SF, Austin, etc.) | Only relevant if the US relocation search narrows to specific cities |
| EURES | Official EU job-mobility portal | Useful if Western-Europe relocation search needs visa/mobility-scheme-aware listings |
| Xing | DACH region (Germany/Austria/Switzerland) | Complements LinkedIn if Germany/Austria specifically become a focus |

Recommendation: don't add all of these at once. Pick 1-2 that match where relocation search actually gets used (e.g. Indeed for broad international + Otta or Wellfound for curated US/EU) and onboard via `/add-portal`; expand later if the ROI is there. Keep the existing rule of not silently scraping a new site without the user's sign-off.

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
- "/scrape big tech" -> run only the career-page checks in the Big-Tech section
