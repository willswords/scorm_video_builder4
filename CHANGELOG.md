# Change log

## 2026-10-21

### Video player

- **Fullscreen has a "video only" step on desktop computers.** The Fullscreen button now cycles through three states:
  1. **First click:** fullscreen with the title, controls, and progress showing (as before).
  2. **Second click:** fullscreen with only the video. Captions still show, and Space and the arrow keys still work.
  3. **To leave video-only:** move the mouse and an **Exit fullscreen** button fades in at the top right. It and the mouse pointer hide again after 2.5 seconds without movement. Esc also exits.
- When the video finishes in video-only mode, the controls and status come back, so the learner can see whether they're complete.
- On touch screens there's no mouse to bring the exit button back, so Fullscreen stays a simple on/off toggle there.

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

### New: SCORM Test LMS (`scorm-test-lms/` folder)

- A local test LMS for checking SCORM 1.2 packages before uploading them, including packages from other tools. Run `node scorm-test-lms.js`, then drop a `.zip` onto the page or pass a zip or folder on the command line. See `scorm-test-lms/README.md`.
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
