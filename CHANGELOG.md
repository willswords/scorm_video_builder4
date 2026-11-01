# Change log

## 2026-10-31

### Video player and builder

Packages built before this change keep the old player. To update one, import it into the builder (**Start from an existing package**) and build it again.

- **The "Mark as complete" button can wait until part of the video is watched.** When **Allow learner to mark themselves complete** is ticked, a new slider sets when the button appears: from the start (the default, as before) or after a chosen percentage has been watched. Skipped parts don't count, the same as for the pass threshold. The value must be below the pass threshold. It's saved as `<self-complete-after>` in `config.xml` (0 = from the start); a `config.xml` without it shows the button from the start.
- **The "Mark as complete" button is now above the Watched / Needed / Status bar**, instead of below it.
- **The video no longer moves while the learner watches.** The player used to be centred vertically in the window, so whenever something appeared or disappeared below the video (the resume message, the "Mark as complete" button, the completion or "not finished" banners, the resource links), the video shifted up or down. The player is now aligned to the top of the window, and the space for the scrollbar is always reserved, so the video also doesn't narrow when the page becomes long enough to scroll. In fullscreen there's no scrollbar, so no space is reserved there. (An earlier build reserved it in fullscreen too, which showed as a strip down the right edge of the screen.)

## 2026-10-21

### Video player

Packages built before this change keep the old player. To update one, import it into the builder (**Start from an existing package**) and build it again.

- **Fullscreen has a "video only" step on desktop computers.** The Fullscreen button now cycles through three states:
  1. **First click:** fullscreen with the title, controls, and progress showing (as before).
  2. **Second click:** fullscreen with only the video. Captions still show, and Space and the arrow keys still work.
  3. **To leave video-only:** move the mouse and an **Exit fullscreen** button fades in at the top right. It and the mouse pointer hide again after 2.5 seconds without movement. Esc also exits.
- When the video finishes in video-only mode, the controls and status come back, so the learner can see whether they're complete.
- On touch screens there's no mouse to bring the exit button back, so Fullscreen stays a simple on/off toggle there.
- **"100%" now means complete.** The watched percentage is rounded down instead of to the nearest whole number. Before, a learner who had watched 99.5% or more saw "100%" but wasn't reported as complete. On a 10-minute video, up to 3 seconds could be unwatched. The same applied at lower thresholds, for example 89.6% showing as "90%". This applies to the stats, the resume message and the end-of-video message.
- **Videos that stop slightly short can be completed.** Some video files report a length up to a second or so longer than they actually play, so the last moments could never be marked as watched and the learner was stuck just under 100%. When the video plays through to its end, up to the last 2 seconds of its stated length now count as watched. Skipping straight to the end still doesn't count the parts that weren't watched.

### SCORM Test LMS

The SCORM Test LMS now has its own repository: [github.com/willswords/scorm-test-lms](https://github.com/willswords/scorm-test-lms). Its README and the Storyline navigation guide are there too, and later changes to it are recorded in its own [change log](https://github.com/willswords/scorm-test-lms/blob/main/CHANGELOG.md).

If the test LMS was running when you updated its files, restart it (Ctrl+C, then run it again). **Close package** and the temp-file cleanup need the updated server; the other changes only need a page reload.

- **SCORM 2004 support** (2nd, 3rd and 4th editions), alongside SCORM 1.2. The version is detected from the manifest, and only the matching LMS connection is offered. See its [README](https://github.com/willswords/scorm-test-lms#readme) for what it checks.
  - **Data model:** the full 2004 data model, with the 2004 error codes.
  - **Manifest settings:** the completion threshold and passing score from the manifest are applied.
  - **Attempts:** the 2004 attempt rules apply. Leaving without "suspend" starts a fresh attempt next time, and the previous result is shown. **Strict resume** is greyed out for 2004 packages, because the standard already behaves that way.
  - **Navigation:** navigation requests between items are followed.
- **xAPI (Tin Can) support, with a built-in LRS.** Packages with a `tincan.xml` now load, and are launched with the standard parameters (LRS address, login, learner, registration and activity ID).
  - **The LRS:** stores statements, saved state and profiles, and keeps them between restarts. It rejects what a real LRS would and flags it, for example a missing version header or login, a non-IRI verb, a score out of range, or a reused statement ID.
  - **What you see:** the log becomes **LRS requests**, with statements described in plain words, and the learner record shows completion, pass/fail, score, duration and saved state, worked out from the statements.
  - **Reset learner:** starts a new registration and clears saved state.
  - **cmi5:** packages are recognised and turned away with a "not supported yet" message.
- **Storyline testing: unlock navigation, the seekbar and Next/Prev.** For Articulate Storyline packages, the Package panel has a **Storyline testing** section with three checkboxes. Each relaunches the course so the player re-reads its settings.
  - **Free navigation (menu):** switches the menu `flow` between `"restricted"` and `"free"` in both `story_content/frame.xml` and `html5/data/js/frame.js` (the file the player reads).
  - **Unlock seekbar:** serves `frame.js` with the seekbar's `readonly` / `readonlyOnce` set to `false`, so the learner can drag it.
  - **Unlock Next/Prev buttons:** stops the slide triggers that disable Next, Prev and swiping from taking effect. Your packages have 44 to 176 such triggers each.
  - **Your package isn't changed.** Values are swapped only as the files are served. Unticking a box returns to the package's own behaviour, and each newly loaded package starts with its own settings.
  - **Tested** against all four Storyline packages in Downloads: every file still loads as valid Storyline data, and nothing besides the lock settings changes. In the running Case Study 1 course, Next and Prev went from disabled to enabled, and the seekbar from read-only to draggable.
  - **Limits:** a slide whose Next has no destination still won't move on, and buttons a slide hides stay hidden.
- **New guide: how navigation locking works in Storyline.** [`STORYLINE-NAVIGATION.md`](https://github.com/willswords/scorm-test-lms/blob/main/STORYLINE-NAVIGATION.md) explains where Storyline stores the menu, seekbar and Prev/Next locks, how the triggers work, how the test LMS unlocks each one and what it can't unlock. It also includes a survey of the four Storyline packages in Downloads.
- **No false alarm on quick relaunches.** Relaunching a slow-loading course (such as Storyline) before it had connected used to raise "The course never called LMSInitialize" (or the xAPI "never contacted the LRS"). These issues are now only raised if the course had at least 8 seconds. Shorter sessions get a note in the log instead.
- **Collapsible right-panel sections.** Package, Learner record, Issues and the call log each collapse to just their heading with a click, or with Tab and Enter.
  - **Log open:** collapsing other sections gives their room to the call log. For example, the log grew from about 190 to 520 pixels with Package and Learner record collapsed.
  - **Log collapsed:** the open sections grow to show their full contents.
  - **Issues count:** stays visible when Issues is collapsed.
  - **Remembered:** which sections are collapsed is saved in your browser.
- **SCORM version shown in the right panel.** A new **Package** section at the top shows a version badge, plus the manifest's declared version, the LMS connection used, and the number of course items.
- **Close package.** A new button unloads the package, deletes its unpacked files, and returns to the start screen, ready for you to drop in another package. The course's exit code runs first, and learner records are kept, so reopening the package later remembers the learner.
- **Temp files cleaned up.** Unpacked packages left behind when the test LMS was closed without Ctrl+C (for example by closing the terminal window) are now removed the next time it starts.

## 2026-10-19

### Builder

- **Import a package to edit it.** A new **Start from an existing package** section, at the bottom below **Build package**, loads a `.zip` previously made with this tool.
  - **What it loads:**
    - the video and captions
    - title, description, and threshold
    - Mark as complete, captions on by default, and acknowledgement settings
    - theme and links
  - **Then:** change anything and build again. It works with packages from any earlier version of the builder, including zips you've re-zipped yourself.
  - **Memory:** the video is read directly from the zip, not loaded into memory. A 215 MB package imports in about a tenth of a second.
  - **Course ID:** **Keep the original course ID** (on by default) gives the new package the old one's course ID, so your LMS treats it as the same course. This still applies if you change the title. Untick it to make a new course.
  - **Packages without a video:** settings and captions are imported, and you're asked to choose the video. The captions are added once you do.
  - **Other tools' packages:** SCORM packages from other tools, such as Articulate Rise, are rejected with an explanation.
  - **Missing options:** if a setting isn't available, the builder falls back and tells you. For example, the B&M theme isn't in the non-branded builder.

## 2026-10-15

### Builder

- **Pass threshold is remembered.** The builder restores the pass threshold you last set, instead of resetting to 100% each time. It's saved in your browser, so each browser remembers its own setting; the branded and non-branded builders share it.
- **Manifest `version` attribute removed.** `imsmanifest.xml` no longer has `version="1.2"` on `<manifest>`, which wrongly suggested the SCORM version was the course's own version. The attribute is optional, so it was dropped rather than set to "1.0". The SCORM version is still declared by `<schemaversion>1.2</schemaversion>`.

## 2026-10-14

Packages built before these changes keep the old player, so rebuild them to pick these up.

### Video player: what learners see

**Seek bar**
- Hovering over the seek bar shows a preview of the frame at that point, not only while dragging. The video keeps playing undisturbed.
- The total length shows as soon as the video loads, instead of "0:00 / 0:00" until play is pressed.

**Fullscreen**
- The "Learning Management System connected" status line and the "Completion recorded" banner are hidden in fullscreen.
- Fixed: after leaving fullscreen, an empty External Resources panel appeared on modules that have no links.

**Mark as complete**
- When enabled, the button shows as soon as the module opens. It stays hidden for learners already recorded as complete.
- Uses the player's own dialog instead of the browser's pop-up. The old pop-up could be blocked inside LMS frames and didn't appear in fullscreen.

**Keyboard and screen-reader access**
- The seek bar can be reached with Tab and works like a slider:
  - Left/Right: 5 seconds
  - Page Up/Down: 30 seconds
  - Home/End: start and end
- Left/Right arrows skip 5 seconds from anywhere on the page.
- Icon-only buttons (play/pause, mute, captions, volume, speed) have screen-reader labels, and these update as their state changes.
- Dialogs move keyboard focus to their buttons, and Escape closes them.
- A visible outline shows which control has keyboard focus.

### Video player: LMS reporting and progress

- **Saved progress no longer wiped on relaunch.** The player now waits for the LMS connection before restoring progress. Before, if the video loaded first, the player could start from zero and overwrite the learner's saved progress in the LMS.
- **Completed learners stay complete.** A learner already passed in the LMS is never sent back to "incomplete" and isn't asked to acknowledge again.
- **Resume support.** Learners who leave before finishing are flagged to resume next time (`cmi.core.exit = "suspend"`). Without this, some LMSs start a new attempt and drop saved progress.
- **Time spent is reported.** Each session sends `cmi.core.session_time`, so LMS reports no longer show 0 time.
- **No calls after exit.** Nothing is sent to the LMS after the session ends.
- **Less LMS traffic.** The connection check runs every 30 seconds while the video isn't playing, instead of every 2. During playback nothing changes.
- **Restore fixes:**
  - Watched sections are restored even if the learner left at the very start.
  - Completion on restore uses the same rule as during playback, so 89.6% no longer rounds up to 90%.
  - If restored progress already meets the threshold, completion is reported to the LMS.
- **Safer text.** Link titles or acknowledgement text containing `</script>` no longer break the player.

### Builder

- **Large videos.** A new built-in zip writer packages videos without loading them into memory. Packaging a 215 MB video used about 1 MB of extra memory.
- **Works offline.** The builder no longer downloads the JSZip library.
- **4 GB limit.** Packages over 4 GB now stop with a clear message.
- **Unique course IDs.** Each course gets its own manifest ID, based on its title. Before, every package shared the same ID, so an LMS could treat a new course as a new version of an old one. Rebuilding the same course keeps its ID.
- **Manifest.** `config.xml` is now listed in the manifest.
- **`config.xml` edits now work as documented:**
  - A blank description hides it.
  - Removing all links removes them.
  - The `<video>` setting, which never worked, was removed.
- **"Save this page to your desktop"** now saves a clean, correctly formatted copy of the builder, not the page in its current state.
- **Non-branded builder.** It's now generated automatically from the branded one, so the two can't drift apart.

### New: SCORM Test LMS (now at [github.com/willswords/scorm-test-lms](https://github.com/willswords/scorm-test-lms))

- A local test LMS for checking SCORM 1.2 packages before uploading them, including packages from other tools. Run `node scorm-test-lms.js`, then drop a `.zip` onto the page or pass a zip or folder on the command line. See its [README](https://github.com/willswords/scorm-test-lms#readme).
- Shows:
  - the course
  - the learner record
  - a list of issues
  - a log of every LMS call, which you can copy
- Checks the course's LMS calls like a real LMS: it rejects invalid values, read-only and write-only fields, and calls outside an open session, with the standard SCORM error codes.
- Applies the pass-mark rule: a score below the package's pass mark is recorded as failed.
- Flags common problems:
  - a course that never finds the LMS
  - no LMSFinish
  - changes never committed
  - no session time
  - leaving incomplete without asking to resume
- Controls:
  - relaunch and end session
  - set the learner's status
  - reset the learner
  - pick between course items in multi-item packages
  - "Strict resume", which copies LMSs that discard progress when a course doesn't ask to resume
