# Semester Forecast

**AppADay 092** &middot; [Live app](https://augustineiacopelli.github.io/appaday-092-semester-forecast/) &middot; [Portfolio](https://augustineiacopelli.github.io/appaday/)

Feed in every syllabus for the term and Claude turns them into one picture: a week by week bar of estimated effort stacked by course, the crunch weeks and the breathing room called out by name, every deliverable with an hours estimate, and the weighted grade math for what is still open.

## What it does

A syllabus tells you what is due. It does not tell you that the week of November 23 is going to break you, because that fact only exists once you lay all four syllabi on top of each other. This app does the laying.

Drop in a syllabus PDF, a photo of a printed schedule, or pasted text. Claude reads it once and returns the whole structure: the grading table, every dated deliverable, the attendance and late work and AI policies, and the things a student could easily miss. On top of the extraction it estimates two things the document never states, which are the hours a typical student spends on each item and how cognitively demanding it is.

Add the next course and it stacks into the same calendar. The forecast strip is the payoff: one bar per week, height set by estimated effort, segmented by course color. Intensity is measured against your own heaviest week rather than an absolute scale, so the bands say where the pressure sits inside this particular term instead of pretending to compare it to anyone else's. Tap any week to see exactly what lands in it.

Four views share the same data. **Forecast** is the week by week shape with headline numbers and named crunch and calm stretches. **Deliverables** is every occurrence grouped by week and filterable by type. **Grades** carries the weighted math forward: enter scores as they come back and each course shows what you have earned, what you have lost, what is still open, and the exact average the remaining work has to carry to land your target. **Policies** collects the fine print. The whole term exports as a printed report or a plain text summary.

## The payoff app

This is the third of a three day arc and it only works because of the two before it.

- **[090 Syllabus Skimmer](https://augustineiacopelli.github.io/appaday-090-syllabus-skimmer/)** established the structured extraction: dates, materials, policies, and watch-outs pulled from an arbitrary syllabus into a fixed JSON shape, with PDF and image attachment handling.
- **[091 Grade Runway](https://augustineiacopelli.github.io/appaday-091-grade-runway/)** established the grade math: weighted breakdowns, the earned-lost-open rail, and the minimum average needed on what remains.

092 runs one extraction that serves both, then adds the thing neither could do alone. Effort estimates plus dates plus multiple courses equals a workload forecast, and a workload forecast is the question a student actually has.

## How the load score works

Each occurrence of an item contributes `hours + (weight * 0.4)` load points, where `weight` is that occurrence's share of the course grade. The multiplier is deliberate: a three hour assignment worth twenty percent of the grade should outweigh a five hour assignment worth nothing, because the pressure of a week is not only how long the work takes. Recurring items divide their total weight across their occurrences, so a discussion post worth twenty percent over fourteen weeks contributes about 1.4 percent per week rather than twenty.

Weeks are Monday-anchored and computed in UTC so the buckets do not drift across time zones. The term spans from the earliest dated item to the latest, with empty weeks kept in place rather than collapsed, because an empty week is information. Intensity bands are set at 25, 50, and 75 percent of the heaviest week: calm, steady, busy, crunch, with a fifth band for weeks that carry nothing at all.

Items the syllabus dates only as "Week 4" cannot be placed on a calendar, so they are excluded from the forecast and listed separately under **Not on the calendar**. They still count toward the grade.

## Entering scores

Scores go in whichever way the grade actually came back. A plain percent like `88` or `88%` is taken as it stands. Raw points off the rubric are converted for you: `22/25`, `17.5 / 20`, and `22 out of 25` all read as the same thing, with the resulting percent shown beside the box so there is no guessing about what was understood. Extra credit is kept rather than clipped, so `26/25` counts as 104 percent and lifts the weighted grade the way it should.

An entry that cannot be read shows a question mark, turns the field red, and is left out of the math entirely rather than being guessed at or silently coerced to zero. That distinction matters: an unparseable line is not a bad grade, it is an unknown one, so it stays out of both the earned points and the graded weight until it is fixed. What persists is the raw entry as typed, not the converted number, so `45/50` still reads as `45/50` when you come back to it and on the printed report.

## Categories with many pieces

A grading line is often a bucket rather than a single thing: ten labs, fourteen discussion posts, three case analyses. The extraction now pulls a `count` and a `drop` off each line, reading language like "10 labs, lowest dropped", "best 12 of 14 count", or "one post per week" across a fourteen week term. Any line with more than one piece gets an expander, and opening it gives you a box per piece.

The category score is the average of what you have entered with the drop rule applied, and the panel says exactly what it did: how many are in, which scores were dropped and what they were, the average of the ones kept, and how much of the category is therefore marked. Nothing about the calculation is hidden behind a number.

The part that matters most is partial completion. Four labs of ten done does not mean the labs category is graded; it means four ninths of it is, because one of the ten will drop out and nine will ultimately count. So a category contributes `weight * (kept / (count - drop))` to the graded denominator and the same fraction of its weight to what you have earned. Without that, doing well on your first two labs would show a badly inflated current grade.

Drops are applied to what has been entered so far, capped so at least one score always survives. That is the optimistic reading, since a low score dropped in September might not be your worst by December, which is exactly why the panel names the dropped score rather than quietly absorbing it.

Entering pieces individually overrides any single score on the category line. The category input is disabled and struck through while pieces are in play, and clearing the pieces hands control back to whatever was there before.

## Extra credit

Lines the syllabus offers as a bonus are marked `extraCredit` during extraction and then held strictly apart from the base. The test the prompt applies is whether the graded lines still total 100 percent without it, which keeps optional-but-required work from being miscategorised.

Extra credit adds to what you have earned and never to what you are measured against. Concretely: it stays out of the weight total, out of the weight bar, out of the sum check that flags a syllabus adding up to 85 percent, and out of the graded denominator. It gets its own gold segment on the runway rail and its own line in the header. A course whose base is exactly 100 with 3 percent of bonus on top shows as `100% +3` on its chip and raises no warning, because nothing is wrong with it.

Unclaimed extra credit is never assumed. Points you have not earned are not points, and a tool that quietly counted them would be telling you that you are safer than you are. What the runway does instead is tell you the bonus is there and what it would be worth: with a 93 percent target, 20 percent graded at 80, and 3 percent of bonus outstanding, it reports that you need a 96.3 percent average on the rest, and that claiming all the extra credit would bring that down to 92.5. Both numbers, and the decision stays yours.

## Grade math

Current grade is earned points plus banked extra credit, divided by graded points, not by the full hundred, so an early quiz is not treated as though it were the whole course. The needed average is `(target - earnedPercent) / openPercent * 100`, scaled against whatever the syllabus weights actually total rather than an assumed hundred. If a syllabus adds up to 85 percent, the app says so on the course chip and on the grade card and then does the math against 85. A needed average over 100 means the target is gone on arithmetic alone, and the app says what the highest reachable finish is instead. At or below zero means the target is already locked in.

## Printing and PDF

**Print or save as PDF** produces a full term report in the app's own visual language rather than a browser dump of whatever tab happens to be open. Every browser's print dialog offers Save as PDF as a destination, so there is no separate export path and no PDF library to ship.

The screen shows one tab at a time; a report has to show all four. So the report is assembled into a separate container that only the print stylesheet reveals, reusing the exact same render functions the screen uses. Paper and screen cannot drift apart, because they are the same code. Report mode changes only what has to change: score fields render as static text instead of live inputs, the on-screen type filter is ignored so the report always carries everything, and controls like filter pills and delete buttons are suppressed.

The output is four sections, each starting on its own page: the forecast strip with its stats and named crunch and calm stretches, every deliverable grouped by week, the grade tables with each course's runway, and the policies. A dated header carries the term label from Settings, and a footer states plainly that hours and difficulty are estimates rather than the syllabus speaking. Colors are forced through with `print-color-adjust: exact`, since a forecast printed in greyscale bars would lose the entire point, and page breaks are pinned so a week row, a course card, or a callout never splits across a page.

The report is rebuilt fresh on every request and discarded whenever the view changes, so a stale term can never reach the printer. `window.print()` is wrapped, because a browser that refuses to open its dialog should produce a useful instruction rather than a dead button.

## Running it

Everything is client side. Bring an Anthropic API key, added through the gear in the top right and kept in `localStorage` only. Syllabus text goes straight from the browser to `api.anthropic.com` with `anthropic-dangerous-direct-browser-access`, and nothing touches a server of mine. Courses and entered scores persist in `localStorage` as well, and the app degrades to a session-only mode if storage is blocked.

**Load sample term** builds a realistic two course fall semester with no key required, and it behaves exactly like real data: the filters, the scores, the runway, and the week drill-in all work.

Model: `claude-sonnet-5`.

## Build notes

Single file, vanilla HTML, CSS, and JavaScript. No frameworks, no build step, no dependency beyond Google Fonts. Fully ASCII source with Unicode written as escapes.

The forecast is DOM and CSS rather than canvas. That was a deliberate choice: every week is a real button, so it is tappable, focusable, and screen-reader addressable for free, and it sidesteps the canvas hazards this project has hit before.

Validated headlessly under jsdom across four suites and 228 assertions covering the runway arithmetic at four boundaries (partial, unreachable, locked in, fully graded), the score parser across eleven accepted formats and eight rejected ones, drop-lowest behaviour at partial and full completion, the graded fraction of an incomplete category, extra credit staying out of every denominator it should, the week bucketing, empty and undated weeks, filters, persistence, course removal, report assembly, and the degradation path when stored data is malformed or corrupt. The parser suite asserts that the same two grades entered as percentages and as points produce byte-identical runway output, which is the property that actually matters.

Two real bugs came out of testing rather than review: a null dereference when the first-run panel was re-read after being destroyed, and a crash on malformed stored courses that jsdom was quietly swallowing until the harness was rewired to surface `jsdomError`. Both are fixed, and stored courses are now sanitized on load so a bad record degrades into a thin course instead of blanking the page.

---

Day 092 of [AppADay](https://augustineiacopelli.github.io/appaday/) by Augustine Iacopelli. One complete, functional, mobile-friendly web app, shipped every single day.
