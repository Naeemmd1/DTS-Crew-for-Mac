# DTS CREW — 34 s animated WhatsApp-Status ad · START HERE (Mac mini M2)

You are Claude, continuing a project for **Diwans Technical Solutions (DTS)**: a web / app / SEO / social-ads / 3D-ads agency in Galiakot, India.
This folder holds everything. Read this file fully, then `STORYBOARD.md` (the **v3** section at the bottom is the APPROVED story), then `HANDOFF.md`.
Work **fully autonomously overnight**. The user is asleep and wants the **finished film by morning**. Don't ask questions: pick sensible defaults, note them in HANDOFF.md, and keep going.

## 0. Ground rules
- Update `HANDOFF.md` after every step (crash-safe log: what's done, the exact next command, file paths).
- Quality bar = the two APPROVED reels in `reference/approved-reels/` ("million-dollar studio ad"). Watch a few frames of each. Same polish: motion on every beat, SFX on every action, beat-snapped music, true motion blur, no dead frames.
- Read `reference/memory/*.md`. These are the user's standing preferences: go all out; never drop an effect because it's hard, find a workaround; flicker/z-fight check before the final render; physical plausibility; keep the handoff updated.
- HARD RULES:
  - ORIGINAL characters only. Never use or recreate real Minions or near-copies: no yellow pill body + goggles + denim overalls + black gloves combo, no real Minion words (banana, bello, poopaye, etc.).
  - Browser-only 3D (Three.js, optionally inside HyperFrames). No Blender modelling.
  - No "claudish" UI overlays (progress bars, timecodes, section labels).

## 1. The characters (user-approved direction)
Three tiny cute shopkeepers. Same appeal as Minions-style sidekicks, but an original design.
- Colours: **TANGERINE body ~#ff8a3d**, **Diwans-BLUE aprons (~#2d5bff, deep #2342c9)**, **NO antennas**, big detailed **amber/brown eyes**, **dark mittens/gloves**, **white sneakers with orange soles**. Brand orange #ff6a2a, cream #f6efe0, ink #1b1d2a.
- **Bip**: smart, tallest, round glasses. Catchphrase "Dee-tee-ess!"
- **Bop**: chubby goofball, one buck tooth.
- **Bloop**: sleepy, half-lids.
- `reference/DTS_Crew_character_sheet_v2.png` is the OLD look (blue, antennas). The user said those "don't look properly made". `reference/DTS_Crew_colour_options.png` option B = the chosen tangerine. `sheet/crew.js` = the old Three.js rig (primitives). Reuse its iris texture, apron logo texture and pose ideas, but REMODEL properly.

### Modelling plan (decided; details in HANDOFF.md "Design decisions")
Python SDF smooth-union sculpt → `skimage.measure.marching_cubes` → Taubin smoothing → decimate (`fast_simplification`) → GLB parts per material via `trimesh` (vertex colours in LINEAR with baked SDF ambient occlusion and blush). Then load with GLTFLoader (already in `sheet/vendor/addons/loaders`).
- Body: torso + head as ONE seamless pear/gourd shape with a soft neck crease, puffy cheeks, tiny button nose. This keeps it distinct from a pill.
- Arms (upper + fore) and legs: separate posable parts with rounded ends that blend at the joints.
- Gloves: thumb + 3 fingers + rolled cuff; pose variants relaxed / point / fist / open.
- Sneakers: sole with toe spring (orange), white upper, padded collar, toe cap, laces + bow, heel tab.
- Apron: real cloth shell with thickness and rounded hem, stitched edges (thread dashes), pocket with a cream **DTS logo patch** (logo SVG path in `sheet/logo_path.txt`, viewBox ≈ 1600×1304, fill-rule evenodd), neck strap, waist tie with a back bow.
- Face: eyes with sclera, iris and scalable pupil, fixed glints and cornea; upper and lower eyelid shells wrapping the eyes (lash line); sculpted dynamic mouth; brows.
- Rendering: premium studio look: key + rim + warm bounce, env reflections (RoomEnvironment), soft shadows + contact shadows, GTAO (`sheet/vendor/addons/postprocessing/GTAOPass.js`), SMAA, filmic/Neutral tonemapping, subtle bloom on glints.

### Face / acting rig (reactions are the heart of the film)
- Eyes: bulge, squint, pupil size, lid open/close/tilt, blinks, saccades.
- Brows: fully expressive (tapered tube along 3 control points projected onto the face).
- Mouth: dynamic contour; interior writes stencil, tongue/teeth stencil-clipped. Visemes A E I O U M/B/P F/V for lip-sync, plus pout, jaw-drop, grin, wobble, smirk.
- Body: squash and stretch (anchored at the feet), anticipation, double takes, face-palm, happy wiggle, side-eye, head turn (2-bone skinned body).

## 2. Tonight's deliverables (the user approved skipping the separate checkpoint, so go straight through)
1. Remodel + rig. Render `DTS_Crew_character_sheet_v3.png` (lineup, expressions, turnaround) and a ~6 s `reaction_test.mp4` (double take, jaw-drop, side-eye, happy wiggle + SFX). Self-review them HARD against the quality bar; fix before moving on.
2. Build the **~34 s film per STORYBOARD v3**, 1080×1920, 9:16:
   - Hook from frame 1: Bop's eye squished at the shutter, it SLAMS up, confetti cannon into the lens.
   - Rule-of-three failed attempts (Bip's sign ignored, Bop's megaphone blasts himself, Bloop's dance → falls asleep), slump + sad trombone.
   - Phone glow in Bip's glasses → "DEE-TEE-ESS!" → the call (DTS logo on the phone).
   - The labelled fix on the beat: **WEBSITE, #1 ON GOOGLE, APP, SOCIAL ADS**.
   - Payoff: every street phone pings, heads snap, customer stampede, Bop flattened then pops up grinning, ka-ching.
   - End card: logo (`brand/logo.png` / `logo_mono.svg`), tagline **"you dream it. we build it."**, services (Websites · Apps · SEO · Social Ads · 3D Ads), **diwanstechsol.com**, numbers **+91 85039 67257 / +91 80032 15329 / +91 80035 53470** (numbers only, no names). Crew waves.
   - Button gag: Bloop snoring on the till, each snore = ka-ching, the other two face-palm.
3. Audio:
   - Original gibberish dialogue: squeaky, sung, fast, three distinct pitched voices (e.g. TTS or recorded syllables pitched up with formant-preserving shift, or synthesized). No real Minion words.
   - Upbeat music on the beat (generate, or compose/synthesize; demucs-check any generated music for stray vocals).
   - SFX on EVERY action: `sfx-lib/` + synthesized cartoon SFX (boing, slide whistle, pop, zip, clang, snore, ka-ching).
   - Mix to -14 LUFS, music ducked under voices.
4. Motion blur: render K sub-frames per frame and blend in linear light (see `reference/pipeline-*/make_mb.py`, `render_mb.cmd`, `finish.sh`). The M2 can afford K=8. Final: 1080×1920; master 60p plus a WhatsApp version (H.264, ~16–20 MB, `+faststart`).
5. QA before delivering: contact sheet every ~1 s; frame-diff flicker/z-fight hunt; still-frame % < 5 %; audio loudness check; no logo or phone-number typos.
6. Deliver to `~/Desktop/DTS_Crew_Ad_WhatsApp.mp4` + `_MASTER.mp4` + the character sheet. Write a short morning summary at the top of HANDOFF.md.

## 3. Mac setup (do first)
- `brew install ffmpeg node python` (if missing)
- `pip3 install numpy scipy trimesh scikit-image pygltflib fast_simplification pyloudnorm soundfile`
- In this folder: `npm init -y && npm i puppeteer-core` (or `npm i puppeteer`, which bundles Chrome). Otherwise use system Chrome at `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`. HyperFrames (`npx hyperframes`) is optional.
- Serve: `python3 -m http.server 8793 --bind 127.0.0.1` from this folder. Pages go in `rig/` and `film/`. `sheet/shot.cjs` is the old screenshot helper; reuse the pattern for frame capture: page exposes `window.renderFrame(t)` and returns the canvas as PNG/JPEG → ffmpeg.
- Long renders: run in the background with `nohup … > log 2>&1 &` and poll the log. Keep the Mac awake: `caffeinate -dimsu &`.
- The Windows-only notes in the reference pipeline (cmd.exe, PowerShell) don't apply on the Mac. Translate them to bash.
