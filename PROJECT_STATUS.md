# SHE TURNS FOUR — project status

A single-file HTML5 canvas horror game, built from a real birthday card drawn by KM. This file is the "pick up where we left off" doc — read this first when reopening the project. For git/GitHub-specific setup (auth, install quirks), see `GITHUB_SETUP.md` (local-only, not in this repo).

## What this is
- `index.html` is the entire game: HTML + CSS + JS in one file, no build step, no dependencies except two Google Fonts and a Supabase leaderboard (public anon key, already in the file, fine to keep public).
- `Messenger_creation_FD88BBDC-B500-418B-B437-A968789A55A8.jpeg` is the original birthday-card photo. **Don't touch this file** — the in-game character sprite is cut out of it using a hand-tuned crop rectangle (`CROP` constant in the JS) that's calibrated to this exact photo's framing/rotation.
- `card-straight.jpg` is a separate, cropped-and-straightened copy of the same photo (wood table removed, deskewed) used only for the decorative `<img>` on the start/end screens. If the original photo ever needs re-cropping for the sprite, this file is independent and won't need to change.
- Repo: https://github.com/KMVVV/Demonic-Princess, branch `main`. Fully pushed and up to date as of the last commit below.

## Current feature set (as of this session)
- **10 levels**, escalating difficulty (speed, battery, light radius, obstacles, timer).
- **Reaper** (unkillable, unstoppable-by-light chaser) arrives from level 3 on, after a countdown.
- **Cake throwing** from level 4 on, **burning cable** from level 8 on — both thrown by the princess, both non-lethal (stun + battery drain on hit, not instant game over). Cake color varies per level.
- Princess has a **throwing-arm swing animation** synced to each throw.
- **Per-level procedural soundtrack** (Web Audio, no audio files) plus level-start stings.
- **Touch controls**: D-pad for movement, and the flashlight is a drag-joystick (independent aim, not tied to movement direction). Only visible during actual gameplay, not on menu/overlay screens.
- **Opening screen**: shows a bouncing, limb-flailing "dance" animation of the princess (shared render code also used on the level-complete screen) plus a demonic Melbourne-bounce-style procedural beat that starts on the first click/keypress anywhere (browsers block audio before a user gesture).
- **Mobile layout**: as of the last commit, the whole page is designed to fit one phone viewport with no scrolling, on both the opening screen and live gameplay (`100dvh` + flexbox, `.stage` sized by available height rather than full width). **This has not yet been visually confirmed on a real phone** — see Known caveats below.
- Player orb: small dull pulsing glow with a crisp bold white ring (redesigned from an earlier much brighter/bigger bloom).
- Princess: dull pulsing presence-glow with a strong bold white outline (same redesign direction).
- Credit line: "a small horror game made from a real birthday card, drawn by KM."

## Real bugs found and fixed this session
1. **Vision-mask bug**: the flashlight/darkness mask was using canvas `destination-out` directly on the main canvas, which *erased* the player sprite to full transparency instead of revealing it through the darkness. Fixed by building the mask on an offscreen canvas buffer first, then compositing it onto the main canvas in one `source-over` draw.
2. **Missing charset**: no `<meta charset="UTF-8">`, which was silently mangling every `·` and `—` character in the UI text into mojibake.

## Known caveats / not yet verified
- **Mobile no-scroll layout is untested on a real device.** The automated browser tooling available this session could not actually change `window.innerWidth` via its resize tool (confirmed via JS: it reported the same desktop width after "resizing"), so the phone layout was built on CSS reasoning + standard techniques (flexbox + `aspect-ratio` + `100dvh`) but never visually confirmed at real phone dimensions. **Check this on an actual phone before assuming it's done.**
- **rAF-driven animations (the dance loops, the main game loop) could not be watched running in real time** via the automated browser this session — that browser tab reported `document.visibilityState === 'hidden'` and `requestAnimationFrame` literally fired zero times per second in it, even after clicking into the tab. This is a testing-tool limitation (backgrounded/occluded tab), not a bug in the game — but it means the dance animations, throw-arm swing, etc. were verified by code review and single-frame rendering checks, not by watching them play smoothly. Worth a live look in a normal browser tab.
- The Supabase leaderboard score cap was raised from 2000 to 10000 to accommodate the new 10-level max score, but nobody's actually beaten all 10 levels yet to confirm the real max score lands under that cap.

## If you're picking this back up
- Git is installed, remote is configured, and there's a cached auth path — see `GITHUB_SETUP.md` for the exact mechanics (PATH quirk for PowerShell tool calls, auth options) before running any git command.
- The user's standing preference: **optimize for phone screens first**, not as an afterthought — check anything new at phone widths (~360–480px) before considering it done.
- Last commit pushed: `197df17` — "Fit whole page in one phone viewport, no scroll on start/gameplay screens".
