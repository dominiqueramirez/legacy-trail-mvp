# Session Handoff — Legacy Trail MVP

_Last updated: 2026-11-13_

## 1. Overview & Tech Stack
**Legacy Trail** (MVP v0.4) is a quiet, respectful 3D walk of remembrance through an early-morning national cemetery featuring 10 real Veterans whose stories appear in **VA News** and the **Veterans Legacy Memorial (VLM)**.

- **Stack**: Zero-build vanilla HTML5, CSS3, and ES Modules with Three.js (`vendor/three.module.min.js`, v0.170.0) and static JSON (`veterans.json`).
- **Design & Tone Rules**: Not a game — no scores, progress counters, timers, celebratory effects, or forced audio. Sound is off by default and only plays if toggled on by the visitor.

---

## 2. How to Run, Deploy & Verify
1. Check if port `8000` is already serving; if not, start a local HTTP server from the workspace root (currently running in background on port `8000`, PID `37654`):
   ```bash
   lsof -i :8000 -sTCP:LISTEN || /usr/bin/python3 -m http.server 8000
   ```
2. Open **http://localhost:8000** in a browser (`fetch('./veterans.json')` and the ES module import map require HTTP rather than `file://`).
3. **GitHub & GitHub Pages**:
   - **Repository**: `https://github.com/dominiqueramirez/legacy-trail-mvp` (remote `origin`, branch `main`)
   - **Live GitHub Pages URL**: `https://dominiqueramirez.github.io/legacy-trail-mvp/` (deploys automatically on push to `main`; keep all asset paths relative so the `/legacy-trail-mvp/` subpath resolves cleanly).

---

## 3. What Was Completed
- **Refined Neck Proportions, Clean Honed Gravestones, Rich Spatial Soundscape & Entrance Return (Latest Session)**:
  - **Shortened Character Neck & Aligned Salute IK**: Shortened `neckGeo` (`y: -0.020..0.074`) and lowered `headPivot.position` to `(0, 0.047, -0.007)` (~2.1 cm reduction) so the jawline and chin sit naturally above the spread dress-shirt collar and necktie knot. Updated `eyeHeight` (`1.634 * heightScale`) and lowered `SALUTE_WRIST_POS` / `SALUTE_POLE` to keep 1st-person camera height and right-brow salute alignment exact.
  - **Clean Honed Marble Gravestones**: Removed procedural quadratic/bezier vein strokes and the crown horizontal divider line from both `createMarbleTexture` and `createInscribedHeadstoneTexture`, replacing them with subtle crystalline mineral speckle on honed white NCA marble. Normalized world-unit `ExtrudeGeometry` UVs across `headstoneExtrudeGeo` (`0..1`), raised arch `curveSegments` to `24`, and added `dirLight.shadow.normalBias = 0.018` to prevent UV-seam streaks and curved-surface shadow-acne lines.
  - **Procedural Spatial Morning Soundscape & Surface-Aware Footsteps (`soundEngine`)**: Replaced the short 18.5s looping `ambient.wav` playback with a non-repeating Web Audio engine (muted by default; toggled via `M` / **Sound**; smoothly ducked via `soundEngine.setDucked(true/false)` during **Play Taps**, with `audio/ambient.wav` kept only as an `AudioContext` fallback):
    - Coprime-length cross-faded stereo noise buffers (`11.3s` & `14.9s`) driving a hushed valley air bed, independent left/right morning breeze swells, proximity-reactive oak/cypress leaf shimmer (`cemeteryTrees`), and spatialized ceremonial flagpole cloth billow + occasional halyard cord/clip taps near `OVERLOOK_CENTER`.
    - Stereo open-air valley impulse-response convolver reverb (`createValleyReverb`) and 3D stereo panning + distance low-pass attenuation (`spatializeXZ`).
    - 7 stochastic dawn bird & valley voices (`voiceMourningDove`, `voiceRobin`, `voiceChickadee`, `voiceSparrow`, `voiceCardinal`, `voiceDistantTrill`, `voiceValleyAccent` passing wingbeats & distant crows) perched on real cemetery trees with recent-voice deduplication, occasional distant answering calls, and rare (every 24–42s) distant bronze sanctuary bells or hushed pastoral harmonic air swells.
    - Gait-synchronized left/right footstep foley (`playFootstep` triggered from `player.walkPhase` heel strikes in `animate()`) adapting via `getWalkSurfaceKind(x, z)` to crushed decomposed-granite `gravel`, clay `brick` borders/gate threshold, and soft dewy `grass`, plus subtle foley on placing tributes (`playTributeFoley`) and raising honor gestures (`playSleeveRustle`).
  - **Cemetery Entrance Spawn, Approach Apron & "End walk" Return**: Added `CEMETERY_ENTRANCE = { x: 0, z: 51.8, yaw: 0, pitch: -0.038 }` and extended `buildTrailSystem` / `distanceToPathXZ` (`ENTRANCE_START_Z = 54.8`) seamlessly south through the stone entrance gate pillars (`z = 47.5`, with wall/pillar collision bounds leaving the 5.1m central gateway open). Clicking **End walk** now calls `endWalk()` (`returnToEntrance()` + `openClosingScreen()`), returning the visitor to the cemetery entrance facing north through the gates while opening the closing screen (*"Is there a Veteran you remember?"* / **Play Taps**) so closing it or clicking **Return to the walk** resumes right at the entrance.
- **Tailored Two-Piece Navy Suit, Spread Dress Collar & Silk Necktie**:
  - Replaced the closed crew-neck / hip-hugging coat silhouette (which looked like a onesie where the belt buckle and thighs poked through) with a **tailored two-piece classic navy worsted suit** (`maleDefault` preset: `outerwearStyle: 'tailoredSuit'`, `outerwearColor: 'classicNavy'`, `shirtColor: 'crispWhite'`, `tieColor: 'burgundySilk'`, `trouserColor: 'classicNavy'`, `footwearColor: 'blackLeather'`).
  - Built `createSuitJacketGeometry` + `createSuitTorsoTextures` (1024×1024 sRGB color + bump map driven by shared metric helpers `getSuitTorsoSlices`, `evalSuitBasePoint`, and `evalSuitFrontZones`): features a 3D-recessed open crisp-white dress-shirt V-zone with lapel AO shadows, flush 3D-rolled notched lapels with angled gorge seam and left Milanese buttonhole, left breast welt pocket with a folded white linen pocket square, two-button single-breasted horn closure, jetted hip flap pockets, single center-back vent, and rounded cutaway front quarters with an inward-turned lining hem that parts cleanly over the belt buckle and upper thighs.
  - Added a sculpted 3D spread dress-shirt collar (`buildDressShirtCollar`) with rear collar reveal and downward front collar points framing a sculpted 3D burgundy reppe-silk Half-Windsor necktie (`buildNecktieMesh` + `createSilkTieTextures`) with knot dimple and tapered blade.
  - Tucked upper-arm sleeve heads cleanly inside the structured suit shoulder crown (eliminating puffy shoulder caps), added 3 kissing horn cuff buttons above crisp white barrel shirt cuffs, added a tucked shirt waist band above a matching calfskin frame-buckle dress belt, sharpened front/back pressed trouser creases, and paired polished black calfskin dress shoes (`buildBootAssembly`).
  - Expanded `CHARACTER_CATALOG` with `'tailoredSuit'`, `'classicNavy'` (`#2c3d55`), `'charcoalSuit'` (`#424852`), `'crispWhite'` (`#f3f0ea`), and `tieColors` (`burgundySilk`, `navySilk`, `slateSilk`, `forestSilk`, `none`), while retaining `knitOverlayer` sweater support and the `createWovenFabricTextures` sRGB color-space fix (`base.getRGB(srgb, THREE.SRGBColorSpace)`).
- **Realistic Male Character & Modular Character Builder Architecture**:
  - Replaced the primitive geometric capsule/sphere visitor avatar in Section 5 of `index.html` with an anatomically proportioned **`1.78m` sculpted & articulated male character** built on a data-driven customization system (`CHARACTER_CATALOG`, `DEFAULT_CHARACTER_CONFIG`, `buildCharacter(customConfig)`, and `window.LegacyTrailCharacterBuilder` with `getConfig()` / `applyConfig(partialConfig)` and full mesh/material disposal via `disposeAvatarObject`).
  - **Parameterized Builder Options** (ready for a future Character Builder UI): `bodyType` (`masculine`, `broad`, `slender`, `feminine` morph scales), `height`, `build`, `skinTone` (6 complexions), `eyeColor` (4 iris colors), `hairStyle` (`texturedSidePart`, `shortCrop`, `wavySwept`, `bald`), `hairColor` (6 shades), `facialHair` (`cleanShaven`, `subtleStubble`, `shortBeard`), `outerwearStyle` (`tailoredSuit`, `tailoredWoolCoat`, `structuredBlazer`, `knitOverlayer`), `outerwearColor`, `shirtColor`, `tieColor`, `sweaterColor`, `trouserColor`, `footwearColor`, and `wristwatch`.
  - **Sculpted Anatomy & Tailored Attire**:
    - Parametric cross-section loft generator (`createSculptedLoft`) with procedural woven wool-twill, suiting torso tailoring maps, reppe-silk tie weave, chino twill, ribbed merino knit, Oxford cotton, burnished leather, directional hair-strand, and 512×512 facial complexion/stubble/eyebrow/lip canvas textures + bump maps (`_charTextureCache`).
    - Vertex-sculpted head (`createSculptedHeadGeometry`: cranium, brow ridge, recessed orbital sockets, nose bridge/tip/nostrils, cheekbones, masculine jaw/chin), sculpted ears (`buildEarMesh`), recessed eyes with sclera/iris/pupil/glint and upper/lower eyelids (`buildEyeAssembly`), and barbered side-parted hair (`createSculptedHairGeometry`).
    - Tailored classic navy two-piece worsted suit over a crisp white spread-collar dress shirt, burgundy silk necktie, barrel shirt cuffs, frame-buckle leather belt, pressed trousers, left-wrist watch, and polished black leather dress shoes/boots (`buildBootAssembly`).
  - **3-Joint Sculpted Hands & Regulation Honor Gesture IK**:
    - Built anatomically articulated hands (`buildHandAssembly` & `setHandCurl`) with a muscular thenar pad, four individual 3-joint fingers (`MCP → PIP → DIP`) with natural inward `-side * Z` curl cascade at rest and straight joined knife-hand alignment during gestures, plus a 3-joint opposable thumb (`CMC → MCP → IP`).
    - Upgraded `solveArmIK` with an orthonormal wrist/palm basis so **Salute** (`1`) orients the straight knife-hand to the right outer brow with palm facing inward/down (`+Z` / `-Y`) and thumb tucked cleanly along the top edge, while **Hand over heart** (`2`) rests the right palm flat over the upper-left chest.
  - **Biomechanical Gait & 360° Idle Third-Person Orbit**:
    - Added 3-joint leg gait (`applyLegGait`: hip flexion/extension, swing-phase knee clearance, heel-to-toe ankle roll), pelvic double-support dip & weight shift, thoracic spine counter-rotation, calm idle breathing, natural arm counter-swing, and smooth head/neck gaze tracking.
    - Standing idle in 3rd-person (`V`) now allows orbiting the camera 360° around the character without forcing `avatarYaw` away from the camera, and triggering gestures away from a headstone preserves the character's current facing so gestures can be viewed from the front.
- **3D National Cemetery Scene (`M-1`)**:
  - Rolling grassy hills (`terrainHeight(x, z)`), painterly grass texture, south stone entrance gates (`z = 47.5`), and a northern ceremonial flagpole overlook (`z = -58.5`).
  - Early-morning atmosphere: custom sky gradient dome shader, `FogExp2` mist, drifting ground-mist wisps, low-angle golden directional sunlight with soft shadows, and toon-shaded oaks and cypresses.
- **NCA-Standard Decomposed-Granite & Clay-Brick Memorial Path**:
  - Researched official **VA National Cemetery Administration (NCA) Design Guide** standards (`FA5 Memorial Walkway` & `Section 10 Civil / Paving`) and built a crowned **5-foot (`1.50m`, `PATH_HALF_WIDTH = 0.75`)** stabilized warm decomposed-granite footpath framed by flush weathered clay-brick stretcher borders (`createTrailTexture`, `buildTrailSystem`), terminating in a circular brick-and-granite overlook pad (`OVERLOOK_RADIUS = 2.85`).
  - Set featured headstones back **`2.75m` (~9 feet, `HEADSTONE_LAWN_SETBACK`)** onto open green turf from the path border (`3.50m` from centerline) with free off-path movement (`walkSurfaceHeight(x, z)`).
- **Featured Headstones, Flags & Carved Inscriptions (`M-4`, `S-5`)**:
  - Upright arched marble headstones with dynamically generated incised front-face canvas textures (`createInscribedHeadstoneTexture`) showing each Veteran's name, branch, war period, and birth–death years, plus gently waving grave-side U.S. flags.
- **Navigation, Cameras & Accessibility (`M-2`, `M-3`, `L-1`, `L-3`)**:
  - `WASD` / Arrow keys movement, mouse look (pointer lock + click-and-drag fallback), mobile virtual joystick + touch drag-to-look, smooth 1st-person ↔ 3rd-person camera blending (`V`), and a **Veterans** directory drawer (`#directory-drawer`) to jump directly to any of the 10 headstones.
- **Memorial Cards (`M-5`, `M-6`)**:
  - Slide-in editorial side panel (`#memorial-card`) displaying the Veteran's VLM portrait (or U.S. Air Force emblem for Lt. Col. Jose Antonio Muniz-Vazquez), rank, branch, era, dates, cemetery, decorations, VA News story summary, and external links to both VA News and VLM.
- **Session Tributes, Audio & Closing Screen (`S-2`, `S-3`, `S-4`)**:
  - Session-persistent 3D **Flag**, **Flower**, and **Stone** tributes (`3` or card buttons) placed at the base of each headstone.
  - Muted by default; `M` toggles the procedural 3D-spatial Web Audio morning soundscape & surface-aware footsteps (`soundEngine`, with `audio/ambient.wav` as fallback).
  - Closing screen (*"Is there a Veteran you remember?"*) triggers on **End walk** (which also returns the visitor to `CEMETERY_ENTRANCE` outside the south stone gates) or after visiting all 10 Veterans, with verified live links to VLM search (`va.gov/remember`), pre-need burial eligibility, and VLM's *Your Story, Your Legacy* (`vlm.cem.va.gov/livingveteranhome`), plus **Play Taps** (`audio/taps.mp3`).

---

## 4. Key Files & Architecture Notes
- `index.html` — Single-file app containing all styles, UI overlays, and 10 modular JS sections (`1. STATE & DOM REFERENCES` → `10. INITIALIZE DATA & WORLD`):
  - Sections 3–4 house terrain, `CEMETERY_ENTRANCE`, `buildTrailSystem`, `getWalkSurfaceKind`, `cemeteryTrees`, and clean honed marble headstone generators (`createMarbleTexture`, `createInscribedHeadstoneTexture`, normalized `headstoneExtrudeGeo`).
  - Section 5 (`MODULAR CHARACTER BUILDER, REALISTIC MALE AVATAR & HONOR GESTURE RIG`) houses `CHARACTER_CATALOG`, `createSuitJacketGeometry`, `createSuitTorsoTextures`, `buildDressShirtCollar`, `buildNecktieMesh`, `buildCharacter`, `CharacterBuilder`, `buildHandAssembly`, `setHandCurl`, and `solveArmIK`.
  - Section 7 (`UI OVERLAYS, AUDIO, DIRECTORY & TRIBUTES`) houses the procedural spatial Web Audio `soundEngine` (`playFootstep`, `playTributeFoley`, `playSleeveRustle`, `setDucked`), `returnToEntrance`, `endWalk`, and modal/drawer state handlers.
- `veterans.json` — Hand-built dataset for the 10 featured Veterans (`ericksen`, `debeeson`, `ruddock`, `reeves`, `muniz`, `raymond`, `monroe`, `ramirez`, `linn`, `wittman`).
- `photos/` — Cached local portraits from VLM plus `air-force-seal.svg` fallback. Portraits are rendered via HTML `<img>` inside `#memorial-card` (not WebGL textures) to avoid CORS issues.
- `audio/` — `taps.mp3` (public-domain U.S. Army Band recording) and `ambient.wav` (fallback ambient track when Web Audio `AudioContext` is unavailable).
- `vendor/three.module.min.js` — Local Three.js v0.170.0 ES module referenced via `<script type="importmap">`.
- `README.md` — Quick-start instructions and controls table.

**Important Gotchas to Preserve**:
1. Keep `visibility: hidden` paired with `visibility: visible` on `.open` / `.visible` states for `#memorial-card`, `#directory-drawer`, `#approach-prompt`, and `.screen-overlay` so hidden dialogs never intercept clicks or screen readers.
2. Preserve pointer-lock vs. non-pointer-lock click handling in `pointerup`: clicking a featured headstone opens its card (and exits pointer lock), whereas clicking open ground/sky when no panel is open engages pointer lock in 1st-person mode.
3. Preserve upward-facing (`+Y`) triangle index winding in `buildTrailSystem` (`indices.push(a, b, c, b, d, c)` on `trailGeo` and `pIdx.push(i0, i1, i2, i1, i3, i2)` on `plazaGeo`) and outward CCW winding in `createSculptedLoft` / `createSuitJacketGeometry` / `buildDressShirtCollar` (`indices.push(a, b, c, b, d, c)`) so WebGL backface culling never hides meshes.
4. **Character & Hand Rig Coordinate Convention**: Character forward is `-Z`, back is `+Z`, right arm is `+X` (`side = +1`), left arm is `-X` (`side = -1`), and limb segments hang along `-Y`. In `wrist` local space, `-side * X` is the palmar inward normal and `-Z` is the radial (thumb/index) edge — so `R_z(-side * angle)` curls fingers inward toward the palm (never `+side`, which hyperextends fingers outward), and `SALUTE_PALM_NORMAL` must keep `+Z` / `-Y` components so the palm faces inward/down with the thumb on top. If neck/torso height changes, keep `eyeHeight` and `SALUTE_WRIST_POS` / `SALUTE_POLE` synchronized with `headPivot.position`.
5. **Garment Silhouettes & Canvas Texture Color Space**: In Three.js v0.170+, `new THREE.Color(hex)` stores linear-sRGB components in `.r/.g/.b`. Whenever writing `THREE.Color` values into `ImageData` on a `CanvasTexture` marked `THREE.SRGBColorSpace` (as in `createWovenFabricTextures`, `createSuitTorsoTextures`, and `createSilkTieTextures`), always extract sRGB bytes via `color.getRGB(out, THREE.SRGBColorSpace)` so midtones/darks are not double-gamma crushed to black. Keep `createSuitJacketGeometry`, `createSuitTorsoTextures`, and `buildNecktieMesh` synchronized through `getSuitTorsoSlices` / `evalSuitBasePoint` / `evalSuitFrontZones`, and keep the jacket hem finished with an inward-turned lining ring (`isBottomLip`) rather than a flat `capBottom` disk so the cutaway front quarters never clip into `hipsSeat` or `beltMesh`.
6. **Headstone Marble Cleanliness & Audio Rules**: Keep `headstoneExtrudeGeo` UVs normalized (`0..1`), avoid drawing procedural vein strokes or divider rules on headstone textures, and preserve `dirLight.shadow.normalBias` so gravestones remain smooth honed white marble. Keep `state.muted = true` on startup and route Taps ducking through `soundEngine.setDucked(...)`.

---

## 5. Next Steps / Ideas for Next Update
- **Character Builder UI & Female / Additional Presets**: Build an editorial slide-in **Customize Visitor** drawer wired directly to `window.LegacyTrailCharacterBuilder.applyConfig(...)`, and expand `CHARACTER_CATALOG` with dedicated female facial/hair/attire sculpting alongside additional hairstyles and civilian attire options.
- **Stronger Painterly Look (`L-2`)**: Add watercolor / brush-stroke / Kuwahara post-processing (`EffectComposer` or custom fullscreen shader), richer sky/foliage brushwork, or subtle morning sunbeams through trees.
- **Headstone Emblems of Belief**: Verify each Veteran's official NCA emblem of belief from their VLM profile and render it in the carved headstone crown in place of the placeholder star.
- **Expanded Branch Representation & Themed Walks (`L-4`)**: Add Coast Guard and Space Force Veterans, or support selectable themed walks (e.g., *Women Who Served*, *Memorial Day*).
- **Guided / Auto-Walk Mode & Environmental Landmarks**: Optional gentle "Follow the path" guided walk button for hands-free live demos, plus stone benches, committal shelter / rostrum landmarks, or seasonal petal drift along the trail.
