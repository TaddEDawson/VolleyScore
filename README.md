# VolleyScore

An offline, single-page volleyball scoreboard for one set at a time. It displays
red and blue scores, identifies the winning side, measures set duration, and
provides a separate two-minute countdown.

All HTML, styling, JavaScript, and seven-segment score graphics are embedded in
[index.html](index.html). No installation, server, build step, external libraries,
downloaded fonts, or internet connection is required.

## Quick start

1. Save [index.html](index.html) to the device you will use for scoring.
2. Open the file directly in a modern browser with JavaScript enabled. A local
   `file://` URL is supported.
3. Check the device's local time, since it controls the clock and automatic
   countdown.
4. Tap or click the upper half of the side that wins a point.
5. Use **New set** (the circular refresh arrows) when you are ready to clear the
   current set.

The scoreboard works with a touchscreen, mouse, or keyboard. Select **Full screen**
(the arrows pointing upper-right and lower-left) to the left of Help to fill the
screen. Select the same icon again or press
**Escape** to leave full-screen mode. If the browser blocks the Fullscreen API,
the page displays a message; use the browser's own full-screen command instead.

## Screen layout

The red and blue panels fill the screen. Controls retain touch targets of at
least 44 by 44 CSS pixels, with transparent backgrounds over the panels. Help
and reset dialogs retain opaque backgrounds for readability.

- **New set** (circular refresh arrows), **Full screen**, and **Help** appear at
  the upper right. The larger set-duration control is at the top without a
  visible label; select it to start the timer.
- Each side has two stopwatch buttons above its score.
- A volleyball button centered above each score selects that side as the first
  server. After selection, its ball is 1.5 times its original size and the other
  ball is half-sized. Both return to their original sizes on reset.
- A horizontal rally strip shows the current set's points in scoring order,
  red or blue for the scoring side, with each square showing that team's score.
  Scroll it to see earlier rallies. There is no visible rally-strip heading;
  its accessible name remains available.
- The **two-minute countdown** is at the bottom center. The larger **local time**
  appears at the top left in 24-hour `HH:MM:SS` format.
- The countdown is dimmed while idle and brightens when started manually or
  automatically at `:58`. It stays bright when it displays **TIMES UP**.
- Countdown guidance is available through its tooltip and accessible description
  rather than a visible line of instructions.
- **Help**, shown as a question mark, opens a quick functionality synopsis and shows
  the app version (**1.0.2**).
- **Statistics**, shown as `#`, opens the current set timeline.
- **Full screen**, shown as diagonal arrows, is immediately to the left of Help.
- A **WINNER** label appears above the winning side after the set ends. Its space
  is centered vertically between the timeout buttons and the score, and
  horizontally above that side's score. Spacing is reserved so announcing or
  clearing a winner does not shift the controls or score panels.

The score panels split the screen equally:

| Side | Appearance |
| --- | --- |
| Left | White score on a red background |
| Right | White score on a dark blue background |

Scores start at `00` and use embedded SVG seven-segment digits. Values below ten
have a leading zero; scores are not capped at two digits, and the graphics resize
for longer values. Each score has at least a 5% margin on both sides of its
panel. The page
is designed to fit the browser viewport without scrolling.

### In-app help

Select **Help** to open an offline, in-page popup summarizing scoring, winning,
timers, reset behavior, and data loss on reload. Close it with **Close** or
**Escape**; keyboard focus returns to the Help button. While the popup is open,
the scoreboard controls are unavailable, but clocks and timers continue running.
Help opens at the top with its heading focused so the synopsis and version are
visible first. Its Close button stays at the top while the content scrolls.

### Set statistics

Select **#** to see a chronological timeline with local event time, event type,
team, rotation (1-6), and score. Choose the first serving team with the volleyball
button above its score, in Statistics before scoring, or when the first point is awarded. Cancelling the
first-server prompt does not award that point. Both teams start at rotation 1.
The team gaining serve rotates forward, wrapping from 6 to 1; consecutive
points by the serving team do not change its rotation.

Point scores are always red first. Timeout entries identify the calling team
and show its score first, saved when the timeout was called. Score corrections
undo that team's latest uncorrected point and recalculate current rotations
from remaining points; earlier timeline snapshots remain unchanged. The first
server cannot change after scoring starts, even if all points are corrected.
The rally strip removes corrected points and recalculates each team's remaining scores.

The timeline records set starts, points, corrections, timeout calls, wins, and
reopened sets. Opening Statistics does not pause timers. A confirmed **New set**
or page reload clears the timeline and first-server choice. See
[ChangeLog.MD](ChangeLog.MD) for release summaries.

## Scoring controls

| Action | Control | Result |
| --- | --- | --- |
| Add a point | Upper half of the red or blue panel | Adds one point to that side |
| Correct a point | Lower half of the red or blue panel | Subtracts one point from that side |
| Start another set | **New set** | Asks for confirmation when scores or timers are in use, then clears them |

The touch targets cover the panel halves, not just the visible symbols or digits,
except where the overlaid controls are tapped. Scores cannot go below `00`.

For keyboard operation, use **Tab** or **Shift+Tab** to focus a control, then
**Enter** or **Space** to activate it. Focused buttons have a visible outline.
Controls and score outputs have accessible labels identifying their team and
action or current score.
Icon-only buttons retain descriptive accessible names and hover tooltips. The
full-screen icon has a dashed border while active and its name changes to
**Exit full screen**.
Unavailable score controls have a dashed outline. Subtraction is
unavailable at zero; additions become unavailable after a win.

## Winning a set and correcting scores

A side wins when both conditions are true:

1. Its score is at least **25**.
2. It leads the other side by at least **two points**.

Examples:

| Score (red-blue) | Result |
| --- | --- |
| `25-23` | Red wins |
| `25-24` | Play continues |
| `25-25` | Play continues |
| `26-24` | Red wins |
| `26-25` | Play continues |
| `25-27` | Blue wins |

After a win, both add-point controls stop accepting points and the winning side
shows **WINNER**. Subtraction remains available on either side so mistakes can
be corrected, provided that side's score is above zero. A screen-reader status
message names the winning side, such as "Red wins the set." Reopening the set
announces that play continues.

The winner is recalculated after every correction. For example, changing
`25-23` to `24-23` removes the winner label, unlocks point additions, and resumes
the set timer. A correction that still leaves a qualifying winner keeps the
set finished.

## Clocks and timers

### Local clock

The top-left clock displays the device's local time as 24-hour `HH:MM:SS`,
without a date. Statistics and set summaries retain localized dates and times.
The clock does not synchronize with an internet time service.

### Set duration

The **Set** timer displays elapsed time as `minutes:seconds`.

- It starts at `0:00` and begins running when the first point is added or the
  set timer is selected. Selecting it while running does not restart it.
- It keeps running during ordinary score corrections, even if both scores
  return to zero.
- It stops when a side wins.
- If a correction reopens the set, it resumes from the accumulated duration;
  time spent in the finished state is not included.
- **New set** clears it to `0:00` and leaves it stopped until the next point or
  manual start.

There is no manual pause button. This timer is independent of the countdown.

### Team timeouts

Each side can use its two stopwatch buttons once per set. Selecting one
starts a 30-second countdown on that button. Only one timeout can run at a time,
and the set timer continues running. Once finished, the used button has grey
text and a dashed border and shows the score when the timeout was called, with the calling team's
score first. For example, a blue timeout at red 10, blue 8 shows `8-10`.
Both timeout icons are restored on **New set**.

### Two-minute countdown

The bottom-center button initially displays `2:00`.
It is dimmed until a manual or automatic countdown starts. Resetting restores
the dimmed idle state; dimming does not prevent an otherwise available manual
start.

**Manual start**

- Before minute `:58` of the hour, tap the button to start a two-minute countdown.
- Tapping while a countdown is active does not pause or restart it.
- When it expires, the button displays **TIMES UP**.
- Outside the automatic countdown window, tapping **TIMES UP** starts another
  two-minute countdown.

**Automatic start**

- Each hour, the page checks for the local-time window from `:58:00` up to the
  next hour and automatically counts down to that hour.
- If the page is opened or resumes updating partway through that window, it
  shows the remaining time, not a fresh two minutes. Opening at `12:59:30`, for
  example, produces approximately `0:30`.
- A manually started countdown still active at `:58` keeps its original end
  time and takes that hour's automatic slot.
- Manual starts are unavailable during minutes `:58` and `:59`, including
  after an early countdown has already expired.

**TIMES UP** remains until another countdown starts, manually or automatically,
or a reset is completed. Expiration is announced once through a polite
screen-reader status message; ticking seconds are not continuously announced.
There is no alarm sound, system notification, score change, or automatic end
to the set.

## Reset behavior

**New set** asks for confirmation if a first server, scores, set duration, countdown, or timeout state
would be lost. **Cancel** is initially focused. Select **Start new set** to
confirm, or **Cancel** or **Escape** to keep the current scores and timers.
Timers keep running while confirmation is open. An unused set resets immediately.

After the set is won, selecting **New set** displays the final score (red first),
start time, end time, set duration, and a running-score step chart in the
confirmation. The chart plots red and blue scores after each point or correction,
including downward steps for corrections. Start time is when
the timer was manually started or the first point awarded; end time is when the
set was won, not when reset was selected. Reopening a set preserves its original
start time and records a new end time when it is won again. Cancelling preserves
the set; confirming clears it. No set history is retained after resetting.

A confirmed reset:

- Restores red and blue scores to `00`.
- Clears the winner labels and unlocks scoring.
- Clears and stops the set-duration timer.
- Cancels the countdown and restores its display to `2:00`.
- Cancels any active timeout and restores both teams' timeout buttons.
- Clears statistics, rotations, and the first-server choice.

If the reset is completed during minutes `:58` or `:59`, that hour's automatic countdown is
suppressed. The countdown stays at `2:00` but cannot be started manually until
the next hour. Future hourly automatic countdowns still work.

## Offline operation and limitations

- Scoring, timers, and graphics work entirely in the open browser page.
- Scores and timer state are held in memory only. Reloading or closing the page
  loses the current set; reopening during `:58` or `:59` can start that hour's
  automatic countdown again.
- Keep the page open and the device awake for a live display. Browsers can
  delay updates in background tabs or while a device sleeps. Timer values are
  calculated from timestamps rather than counting display updates, but no
  alarm is guaranteed while the page is suspended.
- The countdown uses the device's wall clock, so changing that clock can affect
  countdown timing. Set duration uses the browser's monotonic performance
  clock.
- Panel colors, the 25-point target, and the two-point winning
  margin are fixed. There are no configurable team names, 15-point deciding-set
  mode, match-level set totals, past-set history, undo, export, or multi-device syncing.
- Use a current browser supporting CSS Grid, SVG, and
  modern JavaScript APIs. Help and reset confirmation require native HTML dialog
  support.
  No legacy-browser compatibility layer is included.

## Functionality check

To check the page after changes, open [index.html](index.html) directly and verify:

1. Both scores start at `00`; subtracting at zero does nothing.
2. Adding the first point or selecting the set timer starts it; selecting a
   running timer does not restart it.
3. `25-24` remains playable; `26-24` declares a winner and blocks additions.
4. Subtracting from `26-24` to `25-24` reopens scoring and resumes the set timer.
5. A manual countdown starts at `2:00`, cannot restart while active, and ends
   with **TIMES UP** without changing scores.
6. The automatic countdown follows the local `:58` window and respects an
   already-running manual countdown.
7. Cancelling **New set** preserves the current scores and timers; confirming
   clears both scores, the winner, all timers, and used timeouts.
8. Portrait and landscape layouts retain equally sized score panels without
   scrolling, and all controls work with keyboard focus.
9. **Help** appears with the upper-right buttons, opens the synopsis with version `1.0.2`,
   starts at the top, and closes without changing scores or stopping timers.
10. Header controls retain readable text and 44-pixel minimum touch targets.
    Winner and countdown-completion status messages provide useful context
    without continuous timer announcements.
11. Each side can start two timeouts per set; a running timeout blocks the
    others, counts down for 30 seconds, then displays its saved score until reset.
12. After a win, selecting **New set** shows the final score, start and end times,
    and duration before clearing the set.
13. Choose a first server, award points to both sides, and check rotations in
    **#**. Only side-outs rotate a team; corrections recalculate rotations.
14. Check timeout entries show the calling team's saved score first, and that
    cancelling reset preserves statistics while confirming clears them.
15. Select either volleyball before scoring and check the selection is locked
    once scoring starts. Check the rally strip shows each scoring team's running
    score and updates after points and corrections.
16. After a win, check the recap chart follows both scores, including corrections;
    reopening the set hides the recap until the next win.
