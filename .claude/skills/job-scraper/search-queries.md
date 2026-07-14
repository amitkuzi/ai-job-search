# Search Queries for Job Scraper

<!-- Personalized by /setup for Amit Kuzi (2026-07). Target roles: Principal Developer / Tech Lead, Software Architect, Engineering Manager, Technology Consultant. -->

## Search Sites

Two channels (user decision 2026-07-14 - amends the earlier "LinkedIn only" decision):

1. **linkedin.com/jobs** - via the `linkedin-search` CLI skill (location filter: Israel / Tel Aviv metro)
2. **Big-tech & large-enterprise career pages** - direct checks via WebFetch/WebSearch (see section below). No CLI skill exists for these; use `site:` queries or fetch the filtered career-page URLs.

Do NOT add other generic job boards; this setup is intentionally limited to LinkedIn + direct company career pages.

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

### Priority 3: Engineering Manager (hands-on)

People + technology leadership; must keep a hands-on component.

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

## Location Filter

When evaluating results, verify the job location is within commute range from Holon:
- **Ideal:** Holon, Tel Aviv and immediate surroundings
- **Acceptable:** Ra'anana / Herzliya / Kfar Saba in the north down to Rehovot in the south
- **Borderline:** anything up to ~40 min by transit - flag for user judgment
- **Too far:** Netanya and beyond, Haifa, Yokneam, Jerusalem, Beer Sheva (unless remote/hybrid-mostly)
- **Remote/hybrid roles:** PASS regardless of office location if office days are occasional

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
- "/scrape big tech" -> run only the career-page checks in the Big-Tech section
