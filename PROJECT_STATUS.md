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
- **Cake throwing** from level 4 on, **burning candle throwing** from level 8 on — both thrown by the princess, both non-lethal (stun + battery drain on hit, not instant game over). Cake color varies per level. The candle throw tumbles end-over-end in flight (wax stick + trailing flame), renamed from an earlier "burning cable" version that didn't match the game's own candle theme.
- Princess has a **throwing-arm swing animation** synced to each throw.
- **Per-level procedural soundtrack**: an ambient drone/theme (distinct interval, tremolo, dissonant layer per level) plus, layered on top during actual gameplay, a **Melbourne-bounce-style rhythm track** with its own bpm/step-pattern/root-pitch/kick-and-bass waveform per level (`BOUNCE_PARAMS`), plus one unique signature accent voice per level (`LEVEL_ACCENTS`: sparkle, shimmer, metallic clang, distorted stab, candy pluck, sub-bass drop, hollow wood knock, harsh scream, dread growl, rising siren) — all ten rounds sound genuinely distinct, not just "faster." The opening-screen beat has its own signature texture too (`demonGiggle`).
- **Touch controls**: D-pad for movement, and the flashlight is a drag-joystick (independent aim, not tied to movement direction). Only visible during actual gameplay, not on menu/overlay screens.
- **Opening screen**: shows a bouncing, limb-flailing "dance" animation of the princess (shared render code also used on the level-complete screen), a demonic Melbourne-bounce-style procedural beat that starts on the first click/keypress anywhere (browsers block audio before a user gesture), and a row of small animated flickering flames along the bottom (`makeFlameRow`).
- **Mobile layout**: the whole page is designed to fit one phone viewport with no scrolling, on both the opening screen and live gameplay (`100dvh` + flexbox, `.stage` sized by available height rather than full width). **This has not yet been visually confirmed on a real phone** — see Known caveats below.
- Player orb: small dull pulsing glow with a crisp bold white ring.
- Princess: dull pulsing presence-glow with a toned-down white outline (lower alpha/blur than an earlier version that bloomed out her linework detail into a flat white blob).
- Credit line: "a small horror game made from a real birthday card, drawn by KM."

## Real bugs found and fixed
1. **Vision-mask bug**: the flashlight/darkness mask was using canvas `destination-out` directly on the main canvas, which *erased* the player sprite to full transparency instead of revealing it through the darkness. Fixed by building the mask on an offscreen canvas buffer first, then compositing it onto the main canvas in one `source-over` draw.
2. **Missing charset**: no `<meta charset="UTF-8">`, which was silently mangling every `·` and `—` character in the UI text into mojibake.
3. **Leaderboard silently rejected any score over 2000**: the JS-side cap was raised from 2000 to 10000, but the actual Supabase Row-Level Security policy on the `scores` table (a single "Allow public read" policy, misleadingly named, that also gated inserts) and a separate `scores_score_check` table constraint were both still hardcoded to `score <= 2000`. Real 10-level runs easily exceed that. Fixed on the Supabase side: replaced the messy multi-purpose policy with two clean ones (public select, public insert with `0 <= score <= 10000`), and raised the check constraint to match. Confirmed end-to-end with a live 6702-score insert.

## Known caveats / not yet verified
- **Mobile no-scroll layout is untested on a real device.** The automated browser tooling available in these sessions cannot actually change `window.innerWidth` via its resize tool, and separately cannot reliably repaint/composite a backgrounded browser tab (confirmed via `document.visibilityState==='hidden'` and zero `requestAnimationFrame` callbacks/sec, and `canvas.toDataURL()` reads getting blocked by a content filter). So new canvas-drawn visuals this session (the redesigned candle throw, the toned-down princess glow, the flame row) were verified by code review + "no console errors while running" rather than by actually looking at rendered pixels. **Worth a live look in a normal, focused browser tab or real phone before assuming any of this is pixel-perfect.**
- Nobody's actually beaten all 10 levels yet to confirm the real max score lands comfortably under the 10000 cap.

## If you're picking this back up
- Git is installed, remote is configured, and there's a cached auth path — see `GITHUB_SETUP.md` for the exact mechanics (PATH quirk for PowerShell tool calls, auth options) before running any git command.
- The user's standing preference: **optimize for phone screens first**, not as an afterthought — check anything new at phone widths (~360–480px) before considering it done.
- The Supabase project (leaderboard) is on the free tier and can auto-pause after ~1 week of no activity — if a "leaderboard isn't saving" report comes in, hit the REST API directly first to check it's awake before assuming a code regression.
- Last commit pushed: `aa3f8be` — "Update PROJECT_STATUS.md with candle throw, bounce beats, and leaderboard RLS fix".
