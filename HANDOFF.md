# DTS Crew — character ad (crash-safe log)
- 2026-10-08 user wants a Minions-style story (3 shopkeepers, no customers, smart one says "DTS DTS", website+app, customers).
  Refused real/recoloured Minions (IP). Agreed: ORIGINAL trio. Step 1 = character sheet for approval.
- Design: gumdrop bodies in Diwans blue, big eyes (no goggles), orange-ball antenna, orange aprons with DTS mark.
  Bip = smart (round glasses, tall), Bop = chubby goofball (one tooth, bent antenna), Bloop = sleepy (half lids, curly antenna).
- character sheet v5 -> DTS_Crew_character_sheet.png (sheet/crew.js rigs with setPose). awaiting approval
- user rejected v1 trio; showed Minion Bob photo (declined to copy). v2 = chubby soft-toy style: amber detailed eyes, pill bodies on legs, stitched apron+DTS pocket, ink mittens, white/orange sneakers, props. -> DTS_Crew_character_sheet_v2.png awaiting approval
- user picked TANGERINE, no antennas; wants PROPER modelling. Plan: Python SDF sculpt (smooth-union) -> marching cubes -> smooth -> GLB parts per material; rig = body/arms/legs groups; aprons blue.

## Session 2 (2026-10-08) — checkpoint 1: remodel + face rig + reaction test
Env: PPT=C:/Users/burha/AppData/Local/npm-cache/_npx/702923228c2ce1e6/node_modules/puppeteer-core
     CHROME=C:/Users/burha/.cache/hyperframes/chrome/chrome-headless-shell/win64-152.0.7977.30/chrome-headless-shell-win64/chrome-headless-shell.exe
     serve: python -m http.server 8793 --bind 127.0.0.1 --directory C:/Users/burha/Desktop/dts-crew  (pages under /rig/)
Plan / files (new folder rig/):
- rig/sculpt.py   : SDF sculpt -> marching cubes -> Taubin -> GLB per character (rig/models/<name>.glb) + face depth map json
- rig/crew3.js    : Three.js loader + acting rig (eyes/lids/brows/mouth visemes, squash/stretch, arms/elbows, glove variants)
- rig/sheet.html  : character sheet v3 -> DTS_Crew_character_sheet_v3.png
- rig/test.html + rig/render.cjs : reaction test 1080x1920 frames -> renders/reaction_test.mp4 ; rig/sfx.py synth cartoon SFX
Progress (session 2, stopped at usage limit — NOTHING modelled yet):
- DONE: fetched three r181 addons into sheet/vendor/addons (GLTFLoader, BufferGeometryUtils, GTAOPass+shaders, SMAAPass); pip installed fast_simplification (decimation).
- Machine: 4 cores, 10 GB RAM -> keep SDF grids <=4M pts, float32.
Design decisions (ready to code in rig/sculpt.py):
- Silhouette = PEAR/gourd (not pill): belly ellipsoid + head ellipsoid smooth-union (k~0.3) -> soft neck crease; cheek puffs; tiny button NOSE between eye bottoms (original vs Minions).
  Bip belly c(0,.70,0) r(.55,.44,.50) head c(0,1.42,.02) r(.53,.54,.50) eyeR .19 + round glasses; Bop squat: belly r(.68,.46,.60) head r(.58,.50,.54) bigger cheeks, eyeR .215 (R eye 1.08x), buck tooth; Bloop egg, eyeR .20, half lids.
- skimage marching_cubes(gradient_direction='ascent' for SDF neg-inside; check mesh.volume>0), Taubin 8 iters, decimate, vertex COLOR_0 in LINEAR = colour*baked SDF-AO (iq AO, h=.02+.04i) + blush; material white.
- Apron = shell |sdf_body(y clamped to belly equator, flare) - .03| - .009, masked by 2D outline (bib |x|<.22 y.8-1.02, skirt to hem y~.30); pocket + cream DTS patch (planar UV in JS, logo_path.txt); stitches = capsule dashes along find_contours insets; neck strap + waist tie ring + back bow = capsule-chain SDF. Blue #2d5bff, pocket #2347d6, laces blue.
- Gloves (shared parts.glb, wrist origin, hand down -y, palm -x, thumb +z): rolled cuff torus, palm, thumb + 3 two-segment fingers; variants relaxed/point/fist/open; mirror x for R.
- Sneakers (ankle origin, ground y=-.15): upper toe+heel ellipsoids, padded collar torus, tongue, sole rounded slab w/ toe spring (orange), toe cap shell (off-white), crossing laces + bow, orange heel tab.
- Arms: upper/fore round-cones w/ spherical ends (pivot shoulder ~y.9 x~.40, elbow, wrist); legs hip(+-.22,.30)->ankle.
- Export per char GLB nodes + meta JSON (pivots, neckY, eye centres, face DEPTH MAP z(x,y)+normals via SDF ray-march).
JS rig plan (rig/crew3.js): SkinnedMesh 2 bones (torso/head, weights smoothstep around neck) for head turns; squash group anchored y=0;
 eyes = white + iris cap + scalable pupil cap + fixed glints + cornea; upper/lower lid hemisphere shells with rim (+lash line), rot x / tilt z; bulge = eye scale.
 mouth = dynamic contour (w, open, smile, upper, pout, wobble, asym; visemes A E I O U MBP FV) projected via depth map; interior writes stencil, tongue/teeth stencil-tested; lip tubes; Bop tooth unclipped.
 brows = tapered tube along 3 ctrl pts projected on surface. Then sheet v3 + reaction test (double take, jaw-drop, side-eye, happy wiggle) with synthesized cartoon SFX (numpy) + reel-v2 sfx lib (Desktop/diwans-reel-v2/audio/sfx/lib).

## Moving to the Mac mini M2 (2026-10-09)
Copy this whole dts-crew folder to the Mac (e.g. ~/Desktop/dts-crew), open it in Claude Code there, and say "continue the DTS Crew film per HANDOFF.md".
Mac differences: no cmd.exe; run long renders with `nohup … &` or the Bash run_in_background; get Chrome via `npx hyperframes doctor`/hyperframes' headless shell
or system Chrome (/Applications/Google Chrome.app/Contents/MacOS/Google Chrome); install `pip3 install trimesh scikit-image pygltflib fast_simplification scipy`, `brew install ffmpeg`.
Puppeteer: `npm i puppeteer-core` in the project and set PPT to its path. WebGL on M2 is far faster than the old PC: motion blur (8 subframes) is affordable.
Previous transcript lives only on the Windows PC; everything needed is in STORYBOARD.md + this file + sheet/crew.js.
Reference reels (diwans-reel-v2, diwans-motion-post) + SFX lib: copy Desktop/diwans-reel-v2/audio/sfx/lib too if wanted.
- 2026-10-09: work moves to the user's Mac mini M2 overnight. Self-contained package: Desktop/DTS-Crew-for-Mac (START_HERE.md, PASTE_THIS_PROMPT.txt, sheet/, brand/, sfx-lib/, reference/ reels + pipelines + memories). User approved skipping the checkpoint for the overnight run.
