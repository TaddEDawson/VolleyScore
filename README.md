# VolleyScore

An offline, single-page volleyball scoreboard for one set at a time. It displays
HOME and AWAY scores, identifies the winning side, measures set duration, and
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
5. Use **New set** when you are ready to clear the current set.

The scoreboard works with a touchscreen, mouse, or keyboard. Select **Full screen**
to the left of Help to fill the screen. Select **Exit full screen** or press
**Escape** to leave full-screen mode. If the browser blocks the Fullscreen API,
the page displays a message; use the browser's own full-screen command instead.

## Screen layout

The header occupies the upper 20% of the viewport:

- **New set** is on the left.
- The **two-minute countdown** is in the center.
- The **local clock** and **Set** duration are on the right.
- **Help**, below the set timer, opens a quick functionality synopsis and shows
  the app version (**1.0.0**).
- **Full screen** is immediately to the left of Help.
- A **WINNER** label appears above the winning side after the set ends. Its space
  is reserved throughout the set so announcing or clearing a winner does not
  shift the controls or score panels.

The score panels share the lower 80% equally:

| Side | Label | Appearance |
| --- | --- | --- |
| Left | HOME | White score on a red background |
| Right | AWAY | White score on a dark blue background |

Scores start at `00` and use embedded SVG seven-segment digits. Values below ten
have a leading zero; scores are not capped at two digits, and the graphics resize
for longer values. The page is designed to fit the browser viewport without
scrolling.

### In-app help

Select **Help** to open an offline, in-page popup summarizing scoring, winning,
timers, reset behavior, and data loss on reload. Close it with **Close** or
**Escape**; keyboard focus returns to the Help button. While the popup is open,
the scoreboard controls are unavailable, but clocks and timers continue running.

## Scoring controls

| Action | Control | Result |
| --- | --- | --- |
| Add a point | Upper half of the HOME or AWAY panel, marked `+` | Adds one point to that side |
| Correct a point | Lower half of the HOME or AWAY panel, marked minus | Subtracts one point from that side |
| Start another set | **New set** | Immediately clears scores and both timers |

The touch targets cover the panel halves, not just the visible symbols or digits.
Either side's entire lower half can subtract a point. Scores cannot go below
`00`.

For keyboard operation, use **Tab** or **Shift+Tab** to focus a control, then
**Enter** or **Space** to activate it. Focused buttons have a visible outline.
Controls and score outputs have accessible labels identifying their team and
action or current score.

## Winning a set and correcting scores

A side wins when both conditions are true:

1. Its score is at least **25**.
2. It leads the other side by at least **two points**.

Examples:

| Score (HOME-AWAY) | Result |
| --- | --- |
| `25-23` | HOME wins |
| `25-24` | Play continues |
| `25-25` | Play continues |
| `26-24` | HOME wins |
| `26-25` | Play continues |
| `25-27` | AWAY wins |

After a win, both add-point controls stop accepting points and the winning side
shows **WINNER**. Subtraction remains available on either side so mistakes can
be corrected.

The winner is recalculated after every correction. For example, changing
`25-23` to `24-23` removes the winner label, unlocks point additions, and resumes
the set timer. A correction that still leaves a qualifying winner keeps the
set finished.

## Clocks and timers

### Local clock

The upper-right clock displays the device's local time, including seconds, using
the browser's locale formatting. It does not synchronize with an internet time
service.

### Set duration

The **Set** timer displays elapsed time as `minutes:seconds`.

- It starts at `0:00` and begins running when the first point is added.
- It keeps running during ordinary score corrections, even if both scores
  return to zero.
- It stops when a side wins.
- If a correction reopens the set, it resumes from the accumulated duration;
  time spent in the finished state is not included.
- **New set** clears it to `0:00` and leaves it stopped until the next point.

There is no manual pause button. This timer is independent of the countdown.

### Two-minute countdown

The center button initially displays `2:00`.

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
or **New set** is selected. Expiration is visual only: there is no sound,
notification, score change, or automatic end to the set.

## Reset behavior

**New set** acts immediately, without a confirmation dialog. It:

- Restores HOME and AWAY to `00`.
- Clears the winner labels and unlocks scoring.
- Clears and stops the set-duration timer.
- Cancels the countdown and restores its display to `2:00`.

If reset occurs during minutes `:58` or `:59`, that hour's automatic countdown is
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
- HOME and AWAY labels, colors, the 25-point target, and the two-point winning
  margin are fixed. There are no configurable team names, 15-point deciding-set
  mode, match-level set totals, history, undo, export, or multi-device syncing.
- Use a current browser supporting CSS Grid, SVG, and modern JavaScript APIs.
- The Help popup also requires native HTML dialog support.
  No legacy-browser compatibility layer is included.

## Functionality check

To check the page after changes, open [index.html](index.html) directly and verify:

1. Both scores start at `00`; subtracting at zero does nothing.
2. Adding the first point starts the set timer.
3. `25-24` remains playable; `26-24` declares a winner and blocks additions.
4. Subtracting from `26-24` to `25-24` reopens scoring and resumes the set timer.
5. A manual countdown starts at `2:00`, cannot restart while active, and ends
   with **TIMES UP** without changing scores.
6. The automatic countdown follows the local `:58` window and respects an
   already-running manual countdown.
7. **New set** clears both scores, the winner, and both timers.
8. Portrait and landscape layouts retain equally sized score panels without
   scrolling, and all controls work with keyboard focus.
9. **Help** appears below the set timer, opens the synopsis with version `1.0.0`,
   and closes without changing scores or stopping timers.
