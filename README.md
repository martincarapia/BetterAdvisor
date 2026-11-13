# BetterAdvisor

**Work it out yourself before you bother your advisor.**

Two Claude skills, plus a copy-paste prompt, that do the registration legwork an advisor meeting usually starts with. They find your degree plan and audit, pull every section, check conflicts against your real schedule, rank your options with sources, register you, and put the semester on your calendar.

They're aimed at **working students**, people whose schedule has to fit around a job, fixed meetings and other commitments.

> BetterAdvisor doesn't replace your advisor. It means you show up with the answer already worked out. Your advisor still approves substitutions, updates locked plans, and confirms transfer credit.

| Skill | What it does |
|---|---|
| [`class-registration-planner`](skills/class-registration-planner/SKILL.md) | Finds your degree plan and audit, pulls every section of the courses you need, checks them against your real constraints, ranks the combinations that work, writes up the options, registers you, and follows up with your advisor. |
| [`semester-calendar-setup`](skills/semester-calendar-setup/SKILL.md) | Puts the semester on your calendar: class blocks you can read at a glance (course, topic, instructor's full name), weekly study blocks, academic breaks, and removing class sessions during break weeks. |

## Why these exist

Registering for classes looks simple: pick the courses on your plan, find open sections, click submit. For a student with a full-time job it often goes like this:

- **The planned course isn't offered** the term your plan says it is, and nobody tells you until you search.
- **The degree audit allows alternatives** (for example a different physics sequence) but you have to dig to find them, and sequences can't be mixed.
- **Sections conflict in ways that aren't obvious.** The only evening lecture for one course meets at the same time as every evening lab for another, so a schedule that looks fine course by course falls apart once you combine them.
- **"I can't take daytime classes" is often a preference, not a hard rule.** Once you separate a fixed meeting from a flexible workday, options appear that seemed impossible.
- **Advanced courses often only meet during the workday.** Spotting that early changes how you plan every future semester, not just this one.
- **Portals fight you:** SSO "stale request" pages, holds that only appear at the last step, sessions that time out, and linked lecture/lab sections.
- **Then the calendar work:** getting every class, lab, study session and break into your calendar with enough detail that a glance tells you where to be and what to do.

These skills came out of one real registration that hit every one of these problems. They capture the full method, including the dead ends worth checking quickly, so the next student (or the same student next semester) doesn't have to rediscover it.

## Quick start: copy-paste prompt

No skills installed? Paste this into Claude (or another AI that can use your browser and calendar) and fill in the brackets:

```text
You are my academic advisor's prep assistant. Help me register for [TERM, e.g. Spring 2027] at [SCHOOL].

My constraints:
- Work/fixed commitments: [e.g. job Mon–Fri 8–5, flexible; daily 9:30 AM meeting that cannot move]
- Preference: [e.g. avoid time off work unless needed to finish the degree]
- Anything else: [commute, childcare, other jobs, evenings I'm busy]

Do this, step by step, and show your sources (registration system, degree audit, school web pages):
1. Open my student portal and find my degree plan and degree audit (I'll sign in myself; never enter my password). Tell me which courses the plan lists for this term and which requirements are still incomplete, including any alternatives the audit accepts.
2. Pull every section of those courses for this term (days, times, rooms, seats, instructor, prerequisites, linked labs). If a planned course isn't offered, check the audit's alternatives and the next terms.
3. Check my calendar for recurring commitments and confirm with me which are fixed.
4. Build every schedule combination with no conflicts, checking lecture vs lab for every pair of courses. Rank them by hours away from work, then by how fragmented my days get. Flag anything that blocks future semesters, such as required courses that only meet during work hours.
5. Give me a clear recommendation and a short table of options, plus what doesn't work and why.
6. If I ask: write it up as a document for my advisor meeting, with questions to ask.
7. Only when I say so: register me (I approve Submit), then draft an email to my advisor and send it only after I approve.
8. Then put the semester on my calendar: class blocks titled with course, topic and the instructor's full name; weekly study blocks; official breaks from the academic calendar; and remove class and study sessions during break weeks.

Rules: the degree audit decides what counts, not the course number in my plan. Never assume transfer credit or substitutions; mark them "confirm with advisor". Ask me before any irreversible action.
```

## What the skills do

**class-registration-planner**

1. Opens your school's registration and degree-audit pages from the portal, which avoids SSO errors.
2. Reads your plan and audit, including which requirements accept alternative course sequences.
3. Pulls every section (time, room, seats, instructor, prerequisites) for the courses you need, plus summer and next-term options.
4. Reads your calendar to find work hours and fixed commitments, and asks which ones are hard limits.
5. Builds every combination without conflicts and ranks them by **time away from work**.
6. Optionally writes a full options document for your advisor meeting: constraints, what was tried, what fails and why, and questions to ask.
7. Registers you, and only clicks Submit once you approve.
8. Drafts and (once you approve) sends the email to your advisor, and asks them to update your plan.

**semester-calendar-setup**

1. Builds recurring class events with the course topic and **instructor's full name** in the title, and a description covering the room, CRN, what the class is, and what to bring.
2. Adds weekly study blocks around your work and fixed commitments, with more time for online courses.
3. Adds academic breaks from your school's official calendar as all-day "free" events.
4. Removes class and study sessions during break weeks, accounting for daylight-saving changes.

## Requirements

- Claude with tool access: a browser tool (e.g. Claude in Chrome) for your school's portal, and a calendar connector (e.g. Google Calendar) for the calendar skill.
- Built for schools on **Ellucian Banner 9 Self-Service** and **Ellucian Degree Works**, which many US colleges and universities use. The workflow (plan, audit, sections, constraints, combinations, register) applies anywhere. Only the data-pulling steps are Banner-specific.
- You sign in yourself. **Claude never enters your password.**

## Safety built in

- Registration Submit and sending emails are treated as irreversible. Claude does them only when you clearly approve.
- Your degree audit, not the course number on your plan, is treated as the source of truth for what counts.
- Transfer credit and course substitutions are flagged as "verify with your advisor or registrar first", never assumed.
- Nothing about you is stored in the skills. Your schedule, CRNs and constraints stay in your own conversation and memory.

## Install

**Claude apps (claude.ai / desktop):** download a skill folder, zip it, and upload it under *Settings → Capabilities → Skills*. Or paste the `SKILL.md` contents into a new skill.

**Claude Code:** copy the folders into your skills directory:

```bash
git clone https://github.com/martincarapia/BetterAdvisor.git
cp -r BetterAdvisor/skills/* ~/.claude/skills/
```

Then ask something like *"Help me register for next semester's classes"* or *"Put my spring classes on my calendar."*

## Contributing

Your school uses a different registration system (Workday Student, PeopleSoft, Colleague)? Found a new gotcha? Pull requests are welcome. Keep skills generic: no personal data, no single-school assumptions, unless they're clearly labeled as examples.

## License

MIT, see [LICENSE](LICENSE).
