# Bio-Hazard: Viral Sanitization Protocol - Product Requirements Document

## Game Overview

**Title**: Bio-Hazard: Viral Sanitization Protocol
**Genre**: Action/Strategy Puzzle
**Platform**: HTML5 Canvas (Web, Mobile-optimized)
**Technology**: Pure JavaScript, Web Audio API, HTML5 Canvas

### Core Concept
A physics-based chain-reaction game where players strategically deploy sanitization zones to eliminate viruses. Players must achieve 70% viral elimination across progressively contaminated environments using limited shots.

---

## Game Mechanics

### Primary Gameplay Loop
1. Player has **4 shots per level**
2. Click/tap to deploy a **sanitization zone** (expanding circular explosion)
3. Viruses caught in zones are destroyed and create **chain reaction zones**
4. Each destroyed virus spawns a new sanitization zone
5. Must eliminate **≥70% of viruses** to progress to next level
6. **Early Win**: Level completes immediately if all viruses are eliminated (before using all shots)
7. Failing returns player to previous level

### Early Win System
- **Trigger**: All non-Helper viruses are eliminated
- **Benefit**: Complete level before using all 4 shots
- **Boss Levels**: Must kill boss + all other viruses
- **Helper Levels**: Helper doesn't count - only need to kill all others
- **Scoring**: Still get full points and percentage bonus
- **Examples**:
  - Helper kills all 25 viruses → Instant win with 0-3 shots remaining
  - Chain reaction eliminates all viruses → Instant win
  - Boss killed + last virus dies → Instant win

### Sanitization Zones
- **Normal Zone**: 200px radius (100px on mobile)
- **King Zone**: 400px radius (200px on mobile)
- **Growth Rate**: Expands at 6-8px per frame
- **Lifespan**: Fades after reaching max radius
- **Visual**: Cyan/white glowing radial gradient with pulsing effects
- **Zone Types**:
  - **Player-Created**: Direct click zones - affect ALL viruses including Helper
  - **Chain Reaction**: Zones created by killed viruses - affect all EXCEPT Helper
- **Helper Protection**: Helper virus is immune to chain reaction zones but vulnerable to player clicks

### Targeting System
- **Reticle Display**: Crosshair with corner brackets
- **Zone Preview**: Dashed circle showing sanitization radius
- **Shots Remaining**: Numeric indicator below reticle
- **Cursor Following**: Real-time mouse/touch tracking
- **Mobile Behavior**: Shows on touch, hides after brief delay

---

## Virus System

### Virus Types

#### 1. NORMAL Virus (Base Type)
- **Size**: 12px base radius
- **Speed**: 1.5x base (scales with level)
- **Color Variants**:
  - Acid Green (#39ff14)
  - Bio-Purple (#bf00ff)
  - Toxic Cyan (#00f5ff)
- **Behavior**: Random Brownian motion, flees from cursor within 150px radius
- **Points**: 5 (base)

#### 2. KAMIKAZE Virus ⚠️
- **Special Ability**: **IMMUNE** to flee behavior (fearless)
- **Behavior**: Ignores cursor completely, pure Brownian motion
- **Can be killed**: By direct sanitization zones AND chain reactions
- **Color**: Red (#ff0000)
- **Speed**: 1.5x faster than normal
- **Visual Indicator**: Shield emoji (🛡) with pulsing shield effect
- **Strategy**: Unpredictable movement makes them harder to hit
- **Points**: 5

#### 3. SPLITTER Virus 🧬
- **Special Ability**: Splits into **2 smaller normal viruses** when destroyed
- **Color**: Orange (#ff8c00)
- **Size**: Normal
- **Child Viruses**: 60% original size, normal type
- **Strategy**: Increases total virus count (+2 to quota)
- **Points**: 5

#### 4. TANK Virus 💪
- **Special Ability**: Requires **2 hits** to destroy
- **Color**: Purple (#9932cc)
- **Size**: 1.3x larger than normal
- **Speed**: 0.5x slower (easier to hit)
- **Visual Indicator**: Health bar showing remaining hits
- **Points**: 5

#### 5. SPEED Virus ⚡
- **Special Ability**: Moves **3x faster**
- **Color**: Yellow (#ffff00)
- **Size**: Normal
- **Behavior**: Extremely quick Brownian motion
- **Visual Indicator**: Lightning emoji (⚡)
- **Points**: 5

#### 6. KING Virus 👑
- **Spawn Rate**: 10% chance
- **Size**: 25px radius (2x normal)
- **Color**: Gold (#ffd700)
- **Visual**: Golden crown above virus
- **Zone**: Creates 2x radius sanitization zone (400px)
- **Effects**: Enhanced particle explosion, screen shake
- **Points**: 25 (5x normal)

#### 7. HELPER Virus (White Angel) 🤍
- **Spawn**: Singleton at levels 3, 6, 9 (every 3n level)
- **Special Ability**: Auto-sanitizes any virus it touches
- **Color**: White (#ffffff)
- **Size**: 1.2x larger than normal (14.4px radius)
- **Speed**: 0.8x slower than normal (gentle movement)
- **Behavior**: Pure Brownian motion - **ignores cursor** (cannot be herded)
- **Immunity**: **Immune to chain reaction zones** - only dies from direct player clicks
- **Critical Rule**: Must keep alive!
- **Penalty**: If killed by player, all remaining viruses **DOUBLE** (split into 2)
- **Strategy**: Let it roam randomly and help kill viruses, avoid clicking on it directly
- **Anti-Exploit**: Cannot be manipulated with cursor - moves independently
- **Mechanics**: Helper continues moving and helping after touching viruses
- **Win Quota Exclusion**: Does NOT count towards total virus count or sanitization percentage
- **Early Win**: If Helper kills all other viruses, level completes immediately
- **Visual Indicator**: Glowing white aura, heart emoji 🤍
- **Points**: 0 (helper, not enemy)

#### 8. BOSS Virus (Dark Lord) 👿
- **Spawn**: Singleton at levels 5, 10, 15 (every 5n level)
- **Win Condition**: **MUST** kill boss to complete level (overrides 70% rule)
- **Color**: Dark gray/black (#2a2a2a)
- **Size Stages**:
  - Stage 1: 35px radius (largest virus)
  - Stage 2: 35px radius (same size, darker)
  - Stage 3: 3.5px radius (1/10 size, tiny!)
- **Speed Stages**:
  - Stage 1: Normal speed
  - Stage 2: 2x speed (after 1st hit, angry)
  - Stage 3: 3x speed (3x = cumulative)
- **Health**: 3 hits required
- **Behavior**: Becomes increasingly aggressive and difficult
- **Visual**: Menacing appearance, changes with each stage
- **Points**: 100 (major boss kill bonus)

### Virus AI Behavior

#### Brownian Motion
- Random directional changes every 60-120 frames
- Smooth velocity transitions with easing
- Natural "swimming" movement pattern

#### Panic/Flee System
- **Trigger**: Cursor within 150px radius
- **Response**: Flee in opposite direction at 1.35x speed
- **Visual**: Faster animation, increased eye jitter
- **Exceptions**:
  - Kamikaze viruses never panic (fearless)
  - Helper viruses never panic (independent movement, anti-exploit)

#### Boundary Behavior (Hybrid System)
- **Near-edge (margin zone)**: Soft bounce with velocity dampening
- **Off-screen**: Screen wrapping (appear on opposite side)
- **Prevents**: Getting stuck in corners

### Procedural Generation
Each virus has unique random characteristics:
- **Eye Variations**: 1 large, 2 erratic, or 3-5 tiny eyes
- **Spike Pattern**: 6-12 undulating membrane spikes
- **Rotation**: Individual rotation speed and direction
- **Color Scheme**: Random selection from type palette
- **Animation Phase**: Random starting offset for organic feel

---

## Scoring System

### Base Points
- Normal/Special Virus: **5 points**
- King Virus: **25 points**

### Combo System
- **Activation**: Kill viruses within 2 seconds of each other
- **Decay**: Combo resets after 2-second gap
- **Multipliers**:
  - 1-2 kills: **1x** multiplier
  - 3-4 kills: **2x** multiplier
  - 5+ kills: **3x** multiplier
- **Display**: Shows combo count in stats UI

### Level Completion Bonus
- Formula: `percentSanitized × 15 × level`
- Example: Level 5 at 85% = `85 × 15 × 5 = 6,375 points`

### Chain Tracking
- **Chain Counter**: Tracks consecutive kills in single shot
- **Display**: Shows max chain achieved in level completion

---

## Level Progression

### Level Structure
- **Starting Viruses**: 20 + (level × 5)
  - Level 1: 25 viruses
  - Level 5: 45 viruses
  - Level 10: 70 viruses

### Special Virus Distribution
- **King Virus**: Fixed 10% spawn rate
- **Special Types Rate**: `30% + (level × 5%)` (capped at 60%)
  - Level 1: 30% special
  - Level 6: 60% special (max)
- **Type Distribution** (within special rate):
  - 25% Kamikaze
  - 25% Splitter
  - 25% Tank
  - 25% Speed

### Difficulty Scaling
- **Virus Speed**: `baseSpeed × (1 + (level - 1) × 0.05)`
  - Level 1: 1.0x speed
  - Level 10: 1.45x speed

### Contaminated Locations (10 Levels)
1. **BATHROOM TOILET**
2. **MOBILE PHONE**
3. **COMPUTER KEYBOARD**
4. **DOOR HANDLE**
5. **KITCHEN SINK**
6. **ELEVATOR BUTTON**
7. **ATM MACHINE**
8. **RESTAURANT TABLE**
9. **GYM EQUIPMENT**
10. **SHOPPING CART**

*Cycles repeat after level 10 with increasing difficulty*

### Win/Lose Conditions

#### Victory Conditions
1. **Regular Levels**: ≥70% viruses eliminated (or ALL viruses dead for early win)
2. **Boss Levels**: Boss must be killed (and all other viruses for early win)
3. **Helper Exclusion**: Helper virus does NOT count towards win quota
4. **Early Win**: Level completes immediately if all non-Helper viruses are eliminated (even with shots remaining)

**On Victory:**
- Progress to next level
- Earn completion bonus
- Level number increases
- Total loss counter continues (never resets)

#### Defeat Conditions
1. **Regular Levels**: <70% viruses eliminated after all 4 shots used
2. **Boss Levels**: Boss still alive after all 4 shots used

**On Defeat:**
- Replay same level
- Level decreases by 1 (minimum 1)
- No score penalty
- Total loss counter increments (persists forever)

### Leaderboard System
- **Trigger**: Shows after 3 total losses (and every loss thereafter)
- **Storage**: Local storage (browser-based)
- **Display**: Top 3 default leaders + YOU (sorted by level)
- **Default Leaders**:
  1. Alex - Level 30
  2. Ben - Level 28
  3. Cook - Level 25
- **Your Position**: Shows "YOU ⭐" with your highest level reached
- **Highlight**: Your entry has gold highlighting
- **Purpose**: Motivation and progress comparison
- **Reset**: Loss counter NEVER resets

---

## User Interface

### HUD Elements

#### Score Display (Top-Left)
- Color: Glowing green (#00ff88)
- Font: 28px Courier New (20px on mobile)
- Shadow: Triple-layer glow effect

#### Level Display (Top-Right)
- Color: Glowing magenta (#ff00ff)
- Font: 28px Courier New (20px on mobile)

#### Stats Display (Below Score)
- Shows: `CHAIN: Nx | SANITIZED: X/Y | Nx COMBO`
- Color: Cyan (#00d9ff)
- Font: 16px (14px on mobile)

#### Shots Remaining (Top-Right, below level)
- Color: Gold (#ffbe0b)
- Font: 20px bold

#### Location Banner
- Displays contaminated environment name
- Color: Orange (#ff6b00)
- Animation: Fade in for 3 seconds at level start

#### Instructions (Bottom-Center)
- Text: "CLICK TO DEPLOY SANITIZATION ZONE"
- Target: "Target: 70% Viral Elimination"
- Fades out after first shot

#### Message System
- **Level Complete**: Shows sanitization %, chain count
- **Level Failed**: Shows failure message with %
- **Center-screen**: Large glowing text with backdrop

### Visual Effects

#### Particle System
- **Max Particles**: 300 concurrent (performance limit)
- **Spawn Sources**: Zone deployment, virus death
- **Types**:
  - Sanitization particles (cyan)
  - Virus debris (virus color)
  - King explosion (gold, 50 particles)
- **Physics**: Radial explosion with velocity decay

#### Floating Score Text
- Displays points earned (+5, +25, COMBO bonuses)
- Floats upward with scale increase
- Color matches virus type or combo tier
- Duration: 1 second

#### Screen Shake
- **Intensity Levels**:
  - Zone deployment: 1x
  - Level complete: 2x
  - King virus kill: 3x
- **Duration**: 100ms
- **Animation**: Random X/Y translation

#### Death Animation (3 Phases)
1. **Shrink** (0-33%): Radius reduces to 0
2. **Bleach** (33-66%): Color shifts to white
3. **Dissolve** (66-100%): Fade to transparent

---

## Audio System

### Web Audio API Implementation
- **Context**: AudioContext with oscillator nodes
- **Volume Control**: Gain nodes with exponential ramps
- **Base Volume**: 0.15 (15%)

### Sound Effects

#### Sanitization Zone
- Frequency: 800 Hz
- Wave: Sine
- Duration: 0.15s
- Volume: 0.2

#### Virus Kill (Escalating)
- Frequency: `80 + (chainCount × 5)` Hz (max 160 Hz)
- Wave: Sine
- Duration: 0.1s
- Creates pitch climb effect

#### King Virus Kill
- Dual-tone sawtooth waves
- First: 400 Hz for 0.4s
- Second: 600 Hz for 0.3s (100ms delay)
- Volume: 0.25

#### Level Complete
- Triple-tone triangle waves
- 400 Hz → 600 Hz → 800 Hz
- Delays: 0ms, 150ms, 300ms
- Duration: 0.5s, 0.4s, 0.3s
- Volume: 0.25

---

## Mobile Optimization

### Touch Controls
- **Touch Start**: Shows reticle at touch point
- **Touch Move**: Reticle follows finger
- **Touch End**: Deploys zone, reticle persists briefly
- **Debouncing**: 300ms delay prevents double-taps

### Responsive Adjustments
- **Zone Radius**: 50% smaller (100px vs 200px)
- **Font Sizes**: Reduced across all UI elements
- **Touch Targets**: Full-screen canvas
- **Viewport**: `width=device-width, initial-scale=1.0`
- **Meta Tags**: Prevents text selection, tap highlighting

### Mobile Detection
```javascript
/iPhone|iPad|iPod|Android/i.test(navigator.userAgent) ||
matchMedia("(max-width: 768px)").matches
```

### Performance Optimizations
- **Particle Limit**: Hard cap at 300 particles
- **Faster Decay**: Reduced particle lifespan
- **Batch Rendering**: Loops optimized for mobile GPUs
- **Passive Events**: Disabled for precise touch control

---

## Technical Architecture

### Canvas Setup
- **Rendering**: 2D context with alpha channel
- **Sizing**: Full viewport (100vw × 100vh)
- **Auto-resize**: Reinitializes level on window resize
- **DPR Support**: Handles high-DPI displays

### Game State Management
```javascript
game {
  score, level, viruses[], sanitizationZones[], particles[],
  floatingTexts[], shotsRemaining, totalViruses, sanitizedViruses,
  chainCount, combo, comboMultiplier, comboTimer, time,
  mouseX, mouseY, showReticle, isPlaying, levelCompleting
}
```

### Animation Loop
- **RequestAnimationFrame**: 60fps target
- **Delta Time**: Normalized to 16.67ms (60fps baseline)
- **Performance**: All entities batched in single render pass

### Collision Detection
- **Algorithm**: Distance-squared comparison (avoids sqrt)
- **Efficiency**: Only checks growing zones vs alive viruses
- **Formula**: `(dx² + dy²) <= (r1 + r2)²`

### Class Structure
1. **Virus**: Entity behavior, procedural generation, AI
2. **SanitizationZone**: Explosion physics, collision
3. **Particle**: Debris physics, fade effects
4. **FloatingText**: Score popup animation

---

## Game Balance

### Strategic Depth
- **Kamikaze Viruses**: Force planning around chain reactions
- **Splitter Viruses**: Risk/reward - can help or hurt quota
- **Tank Viruses**: Waste sanitization potential if targeted directly
- **Speed Viruses**: Hard to predict, require leading shots
- **King Viruses**: High value targets that create massive chains

### Difficulty Curve
- Early levels (1-3): Learn mechanics, mostly normal viruses
- Mid levels (4-7): Special virus introduction, speed increase
- Late levels (8+): 60% special viruses, high speed, strategic depth

### Skill Expression
- **Zone Placement**: Predicting virus movement
- **Chain Management**: Maximizing cascade effects
- **Priority Targeting**: Kings and clusters
- **Combo Maintenance**: Quick successive kills
- **Resource Management**: 4 shots must be optimal

---

## Visual Style

### Color Palette
- **Background**: Dark navy gradient (#000814 → #001d3d)
- **Viruses**: Neon bio-hazard colors (green, purple, cyan, red, orange, yellow)
- **UI**: Cyberpunk aesthetic (cyan, magenta, gold accents)
- **Effects**: Bright glowing particles, shadows, halos

### Typography
- **Font**: Courier New (monospace, terminal aesthetic)
- **Effects**: Text shadows with glow, triple-layer for emphasis
- **Case**: ALL CAPS for game text
- **Spacing**: Wide letter-spacing (1-2px)

### Animation Style
- **Organic**: Pulsing, undulating membranes
- **Jittery**: Eye movement, panic states
- **Smooth**: Velocity transitions, zone expansion
- **Impact**: Screen shake, particle bursts

---

## Performance Targets

### Constraints
- **Particle Cap**: 300 maximum
- **Virus Count**: Up to 70 at level 10
- **Zone Count**: Typically 4-20 concurrent (during chains)
- **Frame Rate**: Maintains 60fps on mobile

### Optimizations
- Distance-squared collision detection
- Batch particle updates/draws
- Filter arrays instead of splice in loops (where possible)
- Passive: false only where needed for preventDefault
- Minimal DOM manipulation (updates only on state change)

---

## Data Persistence

### Local Storage System
- **Purpose**: Save player progress across sessions
- **Saved Data**:
  - Current score
  - Current level
  - Highest level reached
  - Total losses counter (never resets)
  - Leaderboard data (top 3 players)
- **Storage Key**: `virusGameSave`
- **Auto-save**: On every level completion/failure
- **Auto-load**: On game initialization
- **Leaderboard Key**: `virusGameLeaderboard`
- **Default Leaderboard**: Alex (30), Ben (28), Cook (25)

### Save Format
```javascript
{
  score: number,
  level: number,
  highestLevel: number,
  totalLosses: number  // Never resets - persists forever
}
```

---

## Future Enhancement Ideas

### Potential Features (Not Currently Implemented)
- Power-ups: Extra shots, larger zones, slow-motion
- Achievements: Perfect levels, massive chains, combo milestones
- Online leaderboards: Server-based global rankings
- Difficulty modes: Easy (60% threshold), Hard (80% threshold)
- More virus types: Teleporter, Shield, Multiplier
- Background images: Currently referenced but not loaded
- Sound toggle: Mute option
- Tutorial: Interactive first-level guide

---

## File Structure

```
/virus-1/
├── index.html          # Complete game (single-file)
├── PRD.md             # This document
└── background-*.0     # Referenced but unused background images
```

### Code Organization (within index.html)
1. **Styles** (lines 8-222): CSS for UI, animations
2. **HTML Structure** (lines 224-236): Canvas and UI containers
3. **JavaScript** (lines 238-1423):
   - Canvas setup & config (238-276)
   - Game state (283-308)
   - Virus types & colors (310-330)
   - Virus class (333-719)
   - SanitizationZone class (722-794)
   - Particle class (797-836)
   - FloatingText class (839-865)
   - Audio system (868-901)
   - Utility functions (903-932)
   - Background system (934-959)
   - Level initialization (962-1016)
   - Input handling (1019-1106)
   - Game loop & collision (1242-1392)
   - Event listeners (1394-1418)
   - Initialization (1421-1422)

---

## Browser Compatibility

### Required Features
- HTML5 Canvas
- Web Audio API
- CSS3 animations
- RequestAnimationFrame
- Touch events
- ES6 JavaScript (classes, arrow functions, template literals)

### Tested Platforms
- Modern browsers (Chrome, Firefox, Safari, Edge)
- iOS Safari (mobile)
- Android Chrome (mobile)

---

## Known Limitations

1. **Background Images**: Referenced but not loaded (fallback to gradient)
2. **No Persistence**: Score/progress resets on refresh
3. **No Pause**: Game runs continuously once started
4. **Audio Autoplay**: May be blocked by browser policies
5. **Single-File**: No asset pipeline or build process
6. **No Accessibility**: No keyboard controls, screen reader support

---

## Success Metrics

### Core Loop Validation
- Players understand mechanics within first level
- 70% threshold creates tension but remains achievable
- Chain reactions feel rewarding and strategic
- Special virus types add meaningful variety

### Engagement Indicators
- Average session length
- Levels completed per session
- Retry rate on failed levels
- Combo frequency and max chains

---

## Version History

- **Current**: Full-featured game with 5 virus types, combo system, mobile support
- **Recent Updates** (from git history):
  - Balancing adjustments
  - Mobile support improvements
  - Flee radius randomization removed (now constant)
  - Background system integration

---

*This PRD documents the complete implementation of Bio-Hazard: Viral Sanitization Protocol as of the current build.*
