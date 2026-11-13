---
name: "semester-calendar-setup"
description: "Use after a student registers for classes to put the semester on their calendar: descriptive class blocks, weekly study blocks, academic breaks, and break-week cleanup."
---

# Semester Calendar Setup

Turns a registered schedule into a calendar the student can read at a glance. Uses a calendar connector (for example Google Calendar `create_event`, `update_event`, `delete_event`). If the student has said which calendar or account to use, use that one.

## 1. Gather the facts first

For each registered section, get (from the registration system, e.g. Banner `getFacultyMeetingTimes` and `getCourseDescription`):
- course code and title, section, CRN, credits
- instructor **full name** and email (labs are often "TBA". Say so, and note the lecture instructor)
- days, start and end time, building and room, term start and end dates
- a one-line plain-English summary of the course description
- fees or materials worth knowing (lab fee, eTextbook, graphing calculator)

Get official term dates from the school's academic calendar (search the school's site for "academic calendar <year>"): first day, holidays (e.g. MLK Day), spring break, last day or finals week, and the gap between terms. If the school doesn't label a winter break, use the gap between the end of fall finals and the first day of spring, and say so.

## 2. Class blocks

One recurring event per meeting pattern (lecture, lab, recitation):
- **Title, readable at a glance:** `COURSE NUM Type: short topic · Prof. Full Name`, e.g. `CS 1610 Lecture: Programming II (OOP & GUIs) · Prof. Jane Doe`.
- **Description:** course and section, CRN, credits; instructor and email; room, days and times, term dates; "What it is" (one to two lines); what to bring or fees; what it pairs with (lab ↔ lecture). If the student works, add a note like when to leave work.
- **Location:** building and room plus campus name.
- **Recurrence:** start on the **first actual meeting date** (the first matching weekday on or after the term start), `RRULE:FREQ=WEEKLY;BYDAY=TU,TH;UNTIL=<last day>T235959Z`.
- Online courses with no meeting time get no class block. Cover them with study blocks instead.
- If blocks were created before registration was final, mark them "pending" and update them after.

## 3. Study blocks

- Check the student's existing calendar so blocks avoid work, fixed meetings, workouts and regular evening or weekend commitments (gigs, events).
- Budget realistically. Students who work full time can't do 2–3 hours per credit. About 10 hours a week for 12 credits is a workable start. Give online courses the most time, since nothing else keeps them on pace.
- Pattern that works: a session right after each lecture day to review and start homework, plus a weekend session to finish and submit. For online courses, "watch lectures and take notes" early in the week and "homework and quizzes" later.
- Title: `Study: COURSE – task (Prof. Name)`. Description: what to do in this block, where (LMS), and what it feeds into.
- Add one short weekly check of every course's LMS for due dates.
- Offer to re-align blocks to real due dates once syllabi are out.

## 4. Academic breaks

- Add breaks as **all-day events marked Free** (`availability: FREE`) so they don't block scheduling. All-day end dates are exclusive: a break Mar 8–14 ends on Mar 15.
- Note the source in the description.

## 5. Clear recurring events during breaks

Cancel single instances instead of editing the series:
- Instance ID format: `<seriesId>_<YYYYMMDDTHHMMSSZ>`, where the timestamp is the instance's **original start time in UTC**.
- Convert local time to UTC carefully, **including daylight-saving changes inside the break** (in the US, DST often starts the second Sunday of March, during many spring breaks).
- Use `notificationLevel: NONE`. Each delete should return `status: cancelled`.
- Do this for both class blocks and study blocks, and also for single holidays like MLK Day if the student wants.

## 6. Wrap up

Summarize in a short table: day, time, and block, then the dates covered and the breaks removed. Note anything still unknown (TBA instructors, finals schedule, syllabus due dates) and offer to update it later.