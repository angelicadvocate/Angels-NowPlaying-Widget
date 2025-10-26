Angels Now-Playing Widget — INSTRUCTIONS

Purpose
-------
This repository contains a small set of HTML/CSS/JS frames used as "Now Playing" browser sources in OBS. The frames read a local `Song.json` and `Artwork.png` file produced by Tuna (or other Now Playing producers) and render artist/song text, album art, and a progress bar.

Important environment note
--------------------------
These files are intended to run as local files inside OBS (except the 00-Template-Editor.html and associated files that will run in a regular web browser) (e.g. use the Browser Source and point it at the frame HTML file path). They are not served by a webserver by default. Because of that:

- XHR/fetch requests to local files (file://) can behave differently depending on the host environment. System Chrome (outside OBS) often blocks file:// requests due to CORS/"origin: null" restrictions; OBS's embedded Chromium sometimes allows them. Expect differences between editing/testing in Chrome and running inside OBS.
- If you need fully correct browser behavior (CORS, service workers, relative network resources), serve the project over HTTP (simple static server such as Python's `http.server`, Node's `http-server`, or a tiny Flask app) during development. This is optional but recommended if you hit mysterious fetch failures in your browser.

Quick tuning and debug flags
----------------------------
You can pass query parameters in the Browser Source URL to enable debugging and alternative scrollers:

- `?debug=1` — temporarily reveals overflow and shows extra debug info in the console for one update cycle.
- `?scroller=js` — forces the JavaScript scroller (margin-left animate) rather than CSS keyframe marquee.

Per-frame CSS tuning
--------------------
The scroller and entry alignment are tuned by per-frame CSS values. Use these in the frame's CSS file (examples in `css/02-NowPlaying-F2-Styles.css`):

- `--scroll-extra` (px): Extra width added when deciding whether the text overflows the container and needs to scroll. Increase if the text should scroll earlier.
- `--scroll-start-offset` (px): How far off-screen the text starts before the JS scroller begins. Tweak this to line the text's entry point with the PNG frame edge.

Also note: the `#content` element's left/right padding directly affects where text visually starts inside the frame. If the text appears to start mid-frame, check `#content { padding: ... }` first — adjusting it is often the simplest fix.

Files of interest
-----------------
- `js/common.js` — shared Now-Playing logic (polling `Song.json`, updating DOM, scroller helpers).
- `css/02-NowPlaying-F2-Styles.css` — example of per-frame tuning and the corrected padding comment.
- `Song.json` — example Tuna output (used for polling).
- `editor_assets/` — editor HTML/CSS templates and the editor's generated CSS code. The editor's "generate CSS" function needs more work to match frame outputs perfectly.

Known issues and recommendations
-------------------------------
- If the title intermittently stops showing in OBS, it's often due to animation race conditions or a fetch failure caused by file:// restrictions. Using the JS scroller (`?scroller=js`) and ensuring robust fetch cache-busting helps (the current `js/common.js` should include cache-busting on polls).
- For large projects or collaboration, consider running a simple local HTTP server to avoid file:// inconsistencies.

Angels Now-Playing Widget — Assistant Instructions

Purpose
-------
This repository contains small HTML/CSS/JS "Now Playing" frames intended to run as Browser Sources in OBS. Frames consume a local `Song.json` and `Artwork.png` (Tuna output or similar) and render artist/song text, album art, and a progress bar.

Environment notes
-----------------
- These files are primarily used directly from the filesystem inside OBS (file://). OBS's embedded Chromium often allows file XHR/fetch, but system Chrome may block file:// requests with CORS/origin="null" errors. Expect different behaviour when previewing in a regular browser.
- If you run into unexplained fetch failures or need full browser features (service workers, proper origins), run a simple local HTTP server for development (e.g., Python `http.server`, Node `http-server`, or Flask).

Debug flags and quick tuning
---------------------------
- Query parameters supported in the Browser Source URL:
	- `?debug=1` — temporarily reveals overflow and logs extra debug info during the next update cycle.
	- `?scroller=js` — forces the JS scroller (margin-left animate) instead of the CSS keyframe marquee.

Per-frame tuning knobs
----------------------
Per-frame CSS variables are used to tune scrolling behaviour. The frame CSS files (e.g. `css/02-NowPlaying-F2-Styles.css`) should expose these values:

- `--scroll-extra` (px): extra width to add when deciding whether text overflows and needs to scroll. Increase to make scrolling trigger earlier.
- `--scroll-start-offset` (px): how far off-screen the text starts before the JS scroller begins. Use this to line up the entry point with the frame artwork edge.

Also: `#content` padding directly affects where text visually begins inside the frame. If text appears to start mid-frame, check and adjust `#content { padding: ... }` first — this fixes alignment in most cases.

Primary files and responsibilities
----------------------------------
- `js/common.js` — shared logic: polls `Song.json`, updates DOM, decides whether to use CSS marquee or JS scroller, progress bar updates.
- `css/*-Styles.css` — per-frame styling and tuning variables. Prefer keeping tuning values here rather than in JS when possible.
- `editor_assets/` — editor templates and the editor's generated CSS. The editor's "generate CSS" function currently produces output that doesn't exactly match the working frame CSS; this is a known follow-up item.

Known issues and mitigations
---------------------------
- file:// fetch restrictions: system Chrome often blocks local file fetches. OBS may allow them; if you need to reproduce production behaviour locally, host files over HTTP.
- Intermittent missing title: commonly caused by animation race conditions or fetch failures. Use the JS scroller (`?scroller=js`) and ensure fetches are cache-busted to reduce race conditions.

Developer workflow notes
------------------------
- Prefer CSS tuning for per-frame alignment (`--scroll-extra`, `--scroll-start-offset`, and `#content` padding).
- Keep `js/common.js` configuration-light. If a frame needs special behavior (e.g., F4), prefer per-frame CSS or a small data attribute on the HTML container — `js/common.js` will read `data-scroll-extra` and `data-scroll-start-offset` as fallbacks.

Future improvements
Angels Now-Playing Widget — Assistant Instructions

Purpose
-------
This repository contains small HTML/CSS/JS "Now Playing" frames intended to run as Browser Sources in OBS. Frames consume a local `Song.json` and `Artwork.png` (Tuna output or similar) and render artist/song text, album art, and a progress bar.

Environment notes
-----------------
- These files are primarily used directly from the filesystem inside OBS (file://). OBS's embedded Chromium often allows file XHR/fetch, but system Chrome may block file:// requests with CORS/origin="null" errors. Expect different behaviour when previewing in a regular browser.
- If you run into unexplained fetch failures or need full browser features (service workers, proper origins), run a simple local HTTP server for development (e.g., Python `http.server`, Node `http-server`, or Flask).

Debug flags and quick tuning
---------------------------
- Query parameters supported in the Browser Source URL:
	- `?debug=1` — temporarily reveals overflow and logs extra debug info during the next update cycle.
	- `?scroller=js` — forces the JS scroller (margin-left animate) instead of the CSS keyframe marquee.

Per-frame tuning knobs
----------------------
Per-frame CSS variables are used to tune scrolling behaviour. The frame CSS files (e.g. `css/02-NowPlaying-F2-Styles.css`) should expose these values:

- `--scroll-extra` (px): extra width to add when deciding whether text overflows and needs to scroll. Increase to make scrolling trigger earlier.
- `--scroll-start-offset` (px): how far off-screen the text starts before the JS scroller begins. Use this to line up the entry point with the frame artwork edge.

Also: the element with id "content" (the container for the text) controls where text visually begins inside the frame. If text appears to start mid-frame, check and adjust its padding first — this often fixes alignment.

Primary files and responsibilities
----------------------------------
- `js/common.js` — shared logic: polls `Song.json`, updates DOM, decides whether to use CSS marquee or JS scroller, progress bar updates.
- `css/*-Styles.css` — per-frame styling and tuning variables. Prefer keeping tuning values here rather than in JS when possible.
- `editor_assets/` — editor templates and the editor's generated CSS. The editor's "generate CSS" function currently produces output that doesn't exactly match the working frame CSS; this is a known follow-up item.

Known issues and mitigations
---------------------------
- file:// fetch restrictions: system Chrome often blocks local file fetches. OBS may allow them; if you need to reproduce production behaviour locally, host files over HTTP.
- Intermittent missing title: commonly caused by animation race conditions or fetch failures. Use the JS scroller (`?scroller=js`) and ensure fetches are cache-busted to reduce race conditions.

Developer workflow notes
------------------------
- Prefer CSS tuning for per-frame alignment (use `--scroll-extra`, `--scroll-start-offset`, and adjust the content container's padding when needed).
- Keep `js/common.js` configuration-light. If a frame needs special behavior (e.g., F4), prefer per-frame CSS or a small data attribute on the HTML container — `js/common.js` will read `data-scroll-extra` and `data-scroll-start-offset` as fallbacks.

Future improvements
-------------------
- Add per-frame JSON config files so frames declare anchors/offsets without code changes.
- Fix the editor's CSS generator so it produces output that matches these frame conventions.
- Add lightweight visual regression tests (requires serving over HTTP during test runs).

Contact/next steps
------------------
If you want the default scroller switched to JS, more debug output added, or frame scaffolds for F3/F4, update the top-level TODO and I will implement them.

— Assistant
