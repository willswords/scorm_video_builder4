# SCORM Video Packager (Version 4)

A browser-based tool that wraps a video file into a SCORM 1.2 package ready to upload to any compatible LMS (Cornerstone, Absorb, SABA, etc.).

## How to use it

1. **Open** `scorm_video_builder4.html` in your local browser (do not open from a network drive or SharePoint — it must be a local file).
2. **Fill in the fields:**
   - **Video file** — the .mp4 (or other format) you want to package
   - **Title & Description** — shown in the LMS course catalog
   - **Completion threshold** — percentage of the video the learner must watch to receive credit (default: 100%)
   - **Captions** — optional .vtt subtitle file
   - **Theme** — choose a visual style for the player
3. **Click Download Package** to save a .zip file.
4. **Upload the .zip** to your LMS as a new SCORM 1.2 course.

## Updating a package you built earlier

1. Open the packager and drop the old `.zip` on **Start from an existing package** at the bottom, or click it to browse. The video, captions, and all settings are loaded.
2. Make your changes. You can replace the video or captions, edit the title or threshold, add links, and so on.
3. Click **Generate SCORM Package**.

Leave **Keep the original course ID** ticked when the new package replaces a course already in your LMS, so the LMS recognises it as the same course. Untick it to make a separate, new course.

Only packages made with this tool can be imported.

## What the learner sees

A video player with playback controls, a progress bar, speed control, fullscreen, and optional captions. When the learner has watched enough of the video to meet the threshold, the course is marked complete in the LMS automatically.

## Files in this folder

| File | Purpose |
|------|---------|
| `scorm_video_builder4.html` | Main packager — Burns & McDonnell branded theme included |
| `scorm_video_builder4-non-branded.html` | Same tool, no B&M branding — use for other clients |
| `index.html` | Pre-built generic player — drop it into a package folder alongside `video.mp4` instead of building through the tool |
| `scorm-test-lms/` | Local SCORM 1.2 test LMS for checking any package before uploading it — see its README |
| `CHANGELOG.md` | What changed in each update |

## Notes

- The packager runs entirely in your browser. Nothing is uploaded to any server.
- Captions are not baked into the package — the player reads `captions.vtt` from the package folder at runtime. To add or update captions after packaging, just replace that file in the zip.
- Package settings (completion threshold, self-complete, acknowledgement) can also be overridden after packaging by editing `config.xml` inside the zip.
