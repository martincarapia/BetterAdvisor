---
name: "class-registration-planner"
description: "Use when a student wants help planning or registering for next semester's classes: finding their degree plan, comparing sections around work/life constraints, and registering."
---

# Class Registration Planner

Helps a college student go from "I need to register" to a registered, conflict-free schedule. It covers finding the degree plan, pulling every section, checking them against the student's real constraints, ranking the combinations, registering, and following up with the advisor. Written for schools on **Ellucian Banner 9 Self-Service + Degree Works**, which many US universities use. The workflow still applies elsewhere.

## Ground rules

- **Ask before you assume constraints.** Work hours, fixed meetings, commute, childcare, tutoring and other jobs decide everything. Confirm which ones are hard ("cannot move") and which are preferences ("would rather not"). Students often state a preference as a hard rule. If no option fits, ask whether it can flex before declaring the plan impossible.
- **Never enter passwords.** If a page asks for a sign-in, stop and let the student sign in.
- **Registration submit and sending email are irreversible.** Do them only when the student's request clearly approves that exact action (sections or CRNs named, "register me", "send it"). Otherwise stage everything and ask.
- **The degree audit is the source of truth** for what counts, not the course number in the plan. Equivalent sequences often exist (for example two different intro physics pairs that both satisfy "lab science").
- **Sequences can't be mixed.** If the audit lists A1+A2 or B1+B2, then A1 must be followed by A2.

## Step 1: Open the right pages

1. Start from the student's portal (Ellucian Experience or similar) and click through its cards and links: Registration / Add-Drop, Degree Works or plan card ("View plan").
2. **Do not paste Banner or Degree Works URLs directly.** SSO (CAS/Shibboleth/SAML) often shows a "Stale Request" page, or a fresh sign-in, when a protected page is opened without going through the portal. If that happens, close the tab and click through the portal link again.
3. If a "Smart Plan" or similar planner is empty, check Degree Works. The advisor-built plan is often there.
4. When the student wants two pages side by side (plan + registration), open each in its own tab and say which is which.

## Step 2: Read the plan and the audit

- From the plan, note the courses planned for the target term, the advisor names, and whether the plan is **Locked** (students can't edit a locked plan; changes go through the advisor).
- Pull the full audit for requirement blocks. In Degree Works the dashboard calls a JSON API from the same origin, which you can fetch from the page:
  - `GET /api/audit?studentId=<id>&school=UG&degree=<BS|BA..>&is-process-new=false&audit-type=AA&auditId=&include-inprogress=true&include-preregistered=true&aid-term=` with `Accept: application/json`
  - Walk `blockArray[].ruleArray[]` recursively. Each rule has `label`, `percentComplete`, and `requirement.courseArray[]` (`discipline` + `number`). Group rules list the acceptable alternatives.
- List what's still incomplete (not 100%). Watch for non-course requirements such as civics or literacy exams, residency, and senior capstone.

## Step 3: Pull every section from Banner

Banner 9 Self-Service exposes JSON endpoints under `/StudentRegistrationSsb/ssb/`. They work with the student's session from inside the Registration tab (`fetch` in page context):

```js
const base='/StudentRegistrationSsb/ssb'; const sid='s'+Date.now();
// 1) list terms
await fetch(base+'/classSearch/getTerms?searchTerm=&offset=1&max=10').then(r=>r.json());
// 2) pick a term for searching
await fetch(base+'/term/search?mode=search',{method:'POST',headers:{'Content-Type':'application/x-www-form-urlencoded'},body:'term=202720&uniqueSessionId='+sid});
// 3) before EACH new search, reset the form or results go stale
await fetch(base+'/classSearch/resetDataForm',{method:'POST'});
// 4) search: by subject+number, by subject only, or by attribute (txt_attribute=XXXX)
const r=await fetch(`${base}/searchResults/searchResults?txt_subject=CS&txt_courseNumber=16100&txt_term=202720&uniqueSessionId=${sid}&pageOffset=0&pageMaxSize=100`).then(r=>r.json());
// r.data[]: courseReferenceNumber, sequenceNumber, scheduleTypeDescription (Lecture/Lab/Distance),
// linkIdentifier, seatsAvailable, maximumEnrollment, instructionalMethod (e.g. ONLNE), faculty[],
// meetingsFaculty[].meetingTime {monday..sunday, beginTime, endTime, building, room, startDate, endDate}
```

Per section, POST `term=<code>&courseReferenceNumber=<crn>` to:
- `/searchResults/getCourseDescription`: description, which usually opens with the prerequisites ("P: ...").
- `/searchResults/getSectionPrerequisites` and `/searchResults/getSectionAttributes`: prerequisites and attributes (gen-ed codes, civics, online).
- `GET /searchResults/getFacultyMeetingTimes?term=..&courseReferenceNumber=..`: instructor full name and email, room, dates.

Tips:
- Keep output compact (one line per section). Tool output gets truncated, so filter by course number ranges.
- A course missing from the target term may be offered in summer or the next fall. Check those terms (often "View Only") too.
- A missing planned course is the most common surprise. Check the alternatives listed in the audit right away.
- **Linked sections:** lecture + lab (or recitation) share a `linkIdentifier` pattern. The student must take one of each.

## Step 4: Gather constraints from the calendar

If a calendar connector is available, list two typical weeks during the semester. Note recurring work hours, fixed meetings, tutoring or other jobs, workouts, and weekend gigs. Confirm with the student which are fixed.

## Step 5: Build and rank combinations

1. For each required course, list every lecture and lab pair that doesn't overlap a fixed commitment.
2. Combine courses and drop any combination with internal overlaps. **Check every pairing: lecture vs lecture, lecture vs lab, lab vs lab.** The classic trap is an evening lecture for one course at the same time as the only evening labs of another.
3. Rank by **time away from work** (include roughly 15 minutes travel each way unless the student gives a commute), then by how fragmented the days get. Short morning blocks are easier to flex than mid-afternoon ones, but check fixed meetings.
4. Also list paths that move a course to a later term. Check whether it is a prerequisite for anything: delaying a prerequisite costs more than delaying a standalone requirement like a lab science.
5. Check prerequisites against what the student has finished or is taking now. A "P:" prerequisite isn't met by a course taken in the same term.
6. Look ahead: if most required upper-level courses only meet during work hours, tell the student early. It affects every future semester, not just this one.
7. Mention enrollment status (credits vs the full-time threshold) only if the student cares about it. Some care only about finishing the degree.

Present results as short tables: course, CRN, days and time, seats, result. Give a clear recommendation.

## Step 6: Write it up (when asked)

If the student wants a document (often a Word file to bring to the advisor), include:
1. Bottom line and recommendation
2. Constraints (hard vs soft)
3. What's already registered and a timeline of what was tried
4. What the degree requires (alternatives from the audit)
5. Options ranked by time away from work, with a sample week grid
6. Ideas checked that don't work, and why
7. Later-term and transfer options (for example a community college equivalent, which must be verified with the registrar first)
8. Heads-up on future scheduling problems
9. Questions for the advisor
10. Sources (registration system, audit, emails, web pages)

When constraints change (a job ends, a new fixed meeting appears), re-run the analysis and regenerate the document. Don't patch only the summary.

## Step 7: Register

1. Registration → Register for Classes → choose term → **Continue**. Holds (training, financial, advising) appear here as an error. Read them out: the hold type, who placed it, and their contact. Most clear soon after the student completes the requirement.
2. **Enter CRNs** tab: type each CRN ("+ Add Another CRN" for more) and check that the course title appears beside it. Then **Add to Summary**.
3. Rows show *Pending*. Click **Submit** only when approved, then confirm every row says **Registered** and check total hours. Read any error rows (time conflict, prerequisite, link error, closed) and resolve them.
4. Banner blocks time conflicts unless both instructors approve an override. Don't plan on skipping a required lab.
5. Banner sessions time out. If a page bounces to the SSO login, return to the portal and click Registration again.

## Step 8: Advisor follow-up

- Draft a short email: what's not offered or the conflict, the proposed substitute and how it fits the audit, and any work constraint. Show it, then send from the student's school email once approved. Find the advisor's address from the directory autocomplete. Confirm it's the right person if there is more than one match.
- After a decision, reply in the same thread: what you registered (course, section, CRN, time) and a request to update a locked plan.
- When the advisor replies, summarize the key decision points and next steps (meeting times, booking links).

## Hand-offs

After registering, offer to set up the semester calendar (class blocks with instructor names and descriptions, study blocks, academic breaks). See the `semester-calendar-setup` skill.