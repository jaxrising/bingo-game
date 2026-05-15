# Bingo Game Implementation Specification

## Project Overview
Create a Super Bowl Bingo game with a 5x5 grid system, animated tile states, and comprehensive prop tracking functionality.

---

## Core Requirements

### Grid Structure
- **5x5 grid** with no X/Y axis labels
- **Center tile (position 3,3)** is always a "free spot" with Super Bowl branding
- **24 remaining tiles** contain individual props that fill when conditions are met
- Include a **Game Strip** component positioned above the grid

### Game Strip Components
The game strip displays live game information and should include:
- **Away team logo** (left side, with star indicator if applicable)
- **Away team abbreviation** (e.g., "LAR")
- **Away team score** (large, prominent)
- **Game clock** (center, showing time remaining)
- **Quarter/Period indicator** (e.g., "1st", "2nd", "HALF")
- **Home team score** (large, prominent)
- **Home team abbreviation** (e.g., "TB")
- **Home team logo** (right side, with star indicator if applicable)
- Visual hierarchy: Score should be most prominent (large font)
- Dark background to contrast with the grid below

---

## Tile Types & Content

### 1. Player Tiles
```
Components needed:
- Player headshot image
- First initial + Last name (e.g., "P. Mahomes")
- Team logo
- Stat category label (e.g., "Rush Yds")
- Stat value with "OVER" indicator (e.g., "49.5 Rush Yds ↑")
- Icon options: upward arrow (↑), upward caret (^), or plus sign (+)
```

### 2. Team Tiles
```
Components needed:
- Team logo
- Team nickname (Pro: "Bills", "Ravens" | College: "Alabama", "Ohio State")
- Stat category label
- Stat value with "OVER" indicator (e.g., "49.5 Rush Yds ↑")
- Same icon options as player tiles
```

### 3. Custom Tiles

**Halftime Performer Tiles:**
```
Components needed:
- Performer headshot
- Full name
- Stat category with OVER indicator
```

**Coin Toss Tile:**
```
Components needed:
- Label: "Coin Toss"
- Selection: "Heads" OR "Tails" (no OVER icon)
```

### 4. Center Free Spot Tile
```
Components needed:
- Generic Lombardi Trophy image (reusable asset)
- "Free Spot" or similar branding
- Always marked as "hit" state
```

---

## Tile States & Visual Design

### State 1: Pre-Lock (Normal)
- Default tile appearance
- Interactive/selectable
- Shows all tile content clearly
- No border indicator

### State 2: In-Progress
- **Yellow/gold border** around the tile (persistent)
- Indicates the prop is actively being tracked and has progress
- Tile remains interactive - tappable to view details
- Border thickness should be prominent but not overwhelming (~2-3px)
- Example: Player has 50/100 rushing yards

### State 3: Pending
- Tile has reached the goal threshold BUT waiting for confirmation
- Shows "Pending" indicator or loading state
- Display current progress (e.g., "101/100")
- **Purpose**: Protect against late negative yardage or stat corrections
- Blue informational tooltip: "Yardage totals can change. Check back for final scoring"
- Progress bar shows slightly over 100% but not yet confirmed
- Circular loading icon or pending indicator next to the stat
- Example from screenshot: "Pending: 100 Rushing Yards" with 101/100 shown

### State 4: Locked & Hit (Complete)
- Tile condition has been met AND confirmed
- Visual indicator (filled/highlighted)
- **Green checkmark** in top-right corner of tile
- **No lock icon needed** (user didn't pick these)
- Progress bar fully filled
- Example from screenshot: M. Stafford tile with green checkmark showing 103/200

### State 5: Locked & Full Row Hit
- Entire row/column/diagonal has hit
- Enhanced visual treatment
- Special highlight/glow effect
- All tiles in the row show completion state

### State 6: Locked & Full Card Hit
- All tiles on card have hit
- Maximum celebration visual state
- Card-wide victory animation

## Tile Interaction & Detail Sheet

### Tile Tap Behavior
- All tiles (except Free Spot) are tappable/interactive
- Tapping a tile opens a **bottom sheet modal** with detailed information

### Detail Sheet Contents
Based on screenshots, the detail sheet should include:

**Header Section:**
- Large player/team headshot or logo (background with team branding)
- Player full name in large text (e.g., "MATTHEW STAFFORD", "BUCKY IRVING")
- Team logo (small) + Position + Jersey Number (e.g., "LAR • QB • #9", "BUC • RB • #7")
- X button (top-right) to close the sheet

**Progress Section:**
- Stat label (e.g., "200 Passing Yards", "Pending: 100 Rushing Yards")
- Current progress vs goal (e.g., "103/200", "101/100")
- Progress bar (horizontal):
  - Orange/yellow when in-progress
  - Green when complete
  - Shows percentage filled
- Info icon (i) for additional context

**State-Specific Messages:**
- **Pending state**: Blue tooltip message:
  - "Yardage totals can change. Check back for final scoring"
  - Circular loading/pending icon next to stat
- **Complete state**: Green checkmark, fully filled progress bar

**Sheet Behavior:**
- Slides up from bottom on tap
- Semi-transparent backdrop behind sheet
- Swipe down or tap X to dismiss
- Sheet height should be ~40-50% of screen height

### 1. Entry Animation
**Tile Unveiling on "My Entries" Tab:**
- Tiles should animate in sequentially or in a wave pattern
- Stagger the animation across the grid
- Smooth reveal animation (fade in, scale, or flip)
- Duration: ~1.5-2 seconds total

### 2. State Change Animations

**Normal → In-Progress (Yellow Border):**
- Border fade-in when first progress is made
- Subtle pulse effect on border to draw attention
- Duration: ~300ms

**In-Progress → Pending:**
- Progress bar color shift from orange to light blue
- Loading/pending icon fade-in next to stat
- Tooltip message slide-in from bottom
- Duration: ~400ms

**Pending → Complete:**
- Progress bar: Light blue → Green transition
- Green checkmark icon scale-in animation (top-right)
- Pending icon fade-out
- Tooltip message fade-out
- Subtle celebration effect (shimmer or glow)
- Duration: ~500-700ms

**Pending → In-Progress (Dropped Below Threshold):**
- Reverse animation from pending
- Progress bar: Light blue → Orange
- Loading icon fade-out
- Tooltip fade-out
- Yellow border pulse to indicate change
- Duration: ~300ms

**Blank → Hit (Legacy):**
- Smooth transition when tile condition is met
- Color change, fill animation, or checkmark appearance
- Duration: ~300-500ms

**Row Completion:**
- When full row/column/diagonal hits
- Row-wide highlight or glow effect
- Cascade effect across tiles in the row
- Duration: ~500-800ms
- Consider sound effect trigger point

**Full Card Completion:**
- Celebratory animation when entire card hits
- Card-wide particle effects, confetti, or pulse
- Duration: ~1-2 seconds
- Most impactful animation in the hierarchy

### 3. Interactive Animations

**Tile Tap:**
- Tile scale down slightly (0.95) on press
- Orange border highlight on tap
- Bottom sheet slide-up animation (300-400ms ease-out)
- Semi-transparent backdrop fade-in behind sheet

**Sheet Dismiss:**
- Sheet slide-down animation (250-300ms ease-in)
- Backdrop fade-out
- Tile border returns to state-appropriate color

---

## Technical Implementation Guidance

### Component Structure
```
BingoGame/
├── BingoGrid.tsx          // Main 5x5 grid container
├── BingoTile.tsx          // Individual tile component with states
├── TileTypes/
│   ├── PlayerTile.tsx     // Player-specific content
│   ├── TeamTile.tsx       // Team-specific content
│   ├── CustomTile.tsx     // Halftime/Coin toss variants
│   └── FreeSpotTile.tsx   // Center Lombardi trophy tile
├── GameStrip.tsx          // Game strip component (scores, clock, teams)
├── TileDetailSheet.tsx    // Bottom sheet modal for tile details
├── ProgressBar.tsx        // Reusable progress bar component
├── TileBorder.tsx         // Border component (yellow/orange/none)
└── animations/
    ├── tileAnimations.ts   // Framer Motion or CSS animations
    ├── gridAnimations.ts   // Grid-level animations
    └── sheetAnimations.ts  // Bottom sheet animations
```

### Data Model
```typescript
interface BingoTile {
  id: string;
  position: { row: number; col: number };
  type: 'player' | 'team' | 'custom' | 'freespot';
  state: 'normal' | 'in-progress' | 'pending' | 'complete' | 'row-complete' | 'card-complete';
  
  // Player tile data
  player?: {
    headshot: string;
    firstName: string;
    lastName: string;
    fullName: string; // For detail sheet
    position: string; // e.g., "QB", "RB"
    jerseyNumber: string; // e.g., "#9"
    teamLogo: string;
    teamAbbreviation: string; // e.g., "LAR"
  };
  
  // Team tile data
  team?: {
    logo: string;
    nickname: string;
    abbreviation: string;
  };
  
  // Custom tile data
  custom?: {
    type: 'halftime' | 'cointoss';
    image?: string;
    name?: string;
    fullName?: string; // For detail sheet
    selection?: 'heads' | 'tails';
  };
  
  // Shared prop data
  stat?: {
    category: string; // e.g., "Passing Yards", "Rushing Yards"
    targetValue: number; // Goal to reach
    currentValue: number; // Current progress
    isOver: boolean; // Whether to show "over" indicator
    threshold?: number; // Safety margin before confirming (e.g., 5 yards)
  };
  
  // Progress tracking
  progress?: {
    percentage: number; // 0-100+
    isPending: boolean; // True when goal reached but not confirmed
    isComplete: boolean; // True when confirmed
    pendingMessage?: string; // e.g., "Yardage totals can change..."
  };
  
  isHit: boolean;
  isTappable: boolean; // All except free spot
}

interface BingoCard {
  id: string;
  tiles: BingoTile[];
  completedRows: number[]; // Track completed rows/cols/diagonals
  isComplete: boolean;
  
  // Stats tracking
  stats: {
    tilesHit: number; // e.g., 3/24
    linesComplete: number; // Number of bingos
    rank?: number;
    winPercentage?: number;
  };
}

interface GameStrip {
  awayTeam: {
    logo: string;
    abbreviation: string;
    score: number;
    hasPossession?: boolean; // Star indicator
  };
  homeTeam: {
    logo: string;
    abbreviation: string;
    score: number;
    hasPossession?: boolean; // Star indicator
  };
  clock: {
    time: string; // e.g., "2:00"
    period: string; // e.g., "1st", "2nd", "HALF", "FINAL"
  };
}
```

### Animation Library Recommendations
- **Framer Motion** (recommended for React): Complex orchestrated animations
- **CSS Animations/Transitions**: Simple state changes
- **GSAP**: If you need timeline-based complex sequences

### Styling Considerations
- Use CSS Grid or Flexbox for 5x5 layout
- Responsive sizing (consider mobile vs desktop)
- **Tile borders**: 
  - Yellow/gold border (2-3px) for in-progress tiles
  - Orange border for actively selected/tapped tiles (see screenshots)
  - No border for normal state
- **Progress indicators**:
  - Green checkmark icon (top-right corner) for complete tiles
  - Circular loading/pending icon for pending state
  - Progress bars: Orange → Green color transition
- **Color scheme** (from screenshots):
  - Background: Dark gray/black (#1a1a1a or similar)
  - Card background: Slightly lighter dark gray
  - Accent colors: Blue (#0066FF brand), Orange (#FF8C00), Green (success), Yellow (progress)
- High contrast for "hit" vs "not hit" states
- Accessibility: Ensure color isn't the only indicator of state
- Consider dark mode support (appears to be dark by default)
- **Typography**:
  - Player names: First initial + Last name on tiles (e.g., "M. Stafford")
  - Detail sheet: Full uppercase name (e.g., "MATTHEW STAFFORD")
  - Stats: Bold, clear numbers with units

---

## Assets Needed

1. **Lombardi Trophy icon/image** (SVG preferred for scalability)
2. **Player headshots** (API or static assets)
3. **Team logos** (consistent sizing)
4. **Icon set** for "over" indicators (↑, ^, +)
5. **Performer headshots** (for halftime tiles)

---

## User Experience Flow

1. User navigates to "My Entries" tab
2. Grid unveils with staggered animation
3. User sees current state of all tiles
4. As game progresses and props hit:
   - Individual tiles animate to "hit" state
   - Rows complete with special animation
   - Full card completion triggers celebration
5. User can view completed patterns and progress

---

## Additional Features to Consider

### State Transition Logic
**Critical: Pending → Complete Threshold**
- When a tile reaches its goal (e.g., 100 rushing yards), it enters "pending" state
- **Threshold/buffer zone**: Add safety margin (suggested 5-10 yards) before confirming
- Purpose: Protect against negative yardage or stat corrections
- Example flow:
  1. Player has 99 rushing yards → In-Progress (yellow border)
  2. Player reaches 100 yards → Pending (loading icon, "101/100")
  3. Player maintains 105+ yards through threshold → Complete (green checkmark)
  4. If player drops below threshold (e.g., loses 6 yards) → Back to In-Progress

**Progress Calculation:**
```typescript
function calculateTileState(current: number, target: number, threshold: number = 5) {
  if (current < target) return 'in-progress';
  if (current >= target && current < target + threshold) return 'pending';
  if (current >= target + threshold) return 'complete';
}
```

### Win Pattern Detection
- **Win pattern detection**: Rows, columns, diagonals
- **Progress indicator**: X/24 tiles hit (displayed at top: "2/24 TILES")
- **Lines/Bingos counter**: "-- LINES/BINGOS HIT" (shown in header)
- **Full card indicator**: Red X icon when no full card yet (see screenshots)
- **Filter/highlight**: Show only active tiles
- **Tile detail modal**: Click tile for more info (implemented via bottom sheet)
- **Real-time updates**: WebSocket or polling for stat updates
- **Multiple cards**: User may have multiple entry cards
- **Card naming**: Display card name (e.g., "Jake's Picks")
- **Settings gear icon**: Top-right for card settings

---

## Testing Checklist

- [ ] All tile types render correctly
- [ ] Center tile is always free spot with trophy
- [ ] Game strip displays correctly with live data (scores, clock, teams)
- [ ] Animations work smoothly on entry
- [ ] State changes animate properly (normal → in-progress → pending → complete)
- [ ] Yellow border appears on in-progress tiles
- [ ] Pending state shows loading icon and tooltip
- [ ] Complete state shows green checkmark
- [ ] Threshold logic works (pending doesn't confirm too early)
- [ ] Reverse transitions work (pending back to in-progress if stat decreases)
- [ ] Tiles are tappable (except free spot)
- [ ] Bottom sheet opens on tile tap
- [ ] Bottom sheet shows correct information (player/team details, progress)
- [ ] Bottom sheet dismisses correctly (swipe down, X button, backdrop tap)
- [ ] Progress bars update in real-time
- [ ] Row completion detected and animated
- [ ] Full card completion works
- [ ] Card header shows correct stats (X/24 tiles, lines/bingos hit)
- [ ] Responsive on mobile and desktop
- [ ] Accessible (keyboard navigation, screen readers)
- [ ] Performance with multiple cards
- [ ] Real-time stat updates work correctly
- [ ] Edge cases: Negative yardage, stat corrections

---

## Visual Reference from Screenshots

### Key Observations:

**Header Section:**
- Title: "Jake's Picks" (card name, customizable)
- Stats row showing:
  - "2/24 TILES" (tiles completed count)
  - "-- LINES/BINGOS HIT" (number of complete lines)
  - Red X icon for "FULL CARD" (indicates not complete)
  - "-- RANK" (user's ranking)
  - "-- PCT" (win percentage)
- Settings gear icon (top-right)

**Game Strip:**
- Away team (left): Logo, "LAR", Score "10"
- Center: "2:00" clock, "1st" quarter indicator
- Home team (right): Score "0", "TB", Logo
- Star indicators on team logos (possession or home/away marker)

**Tile States Shown:**
- **In-Progress**: B. Irving (100 Rush Yds) - Yellow border, 101/100 shown
- **Complete**: M. Stafford (200 Pass Yds) - Green checkmark, 103/200 shown
- **Free Spot**: Center tile with "Free" label and green checkmark

**Bottom Sheet Examples:**

*Matthew Stafford Sheet:*
- Full name: "MATTHEW STAFFORD"
- Team info: "LAR • QB • #9"
- Progress: "200 Passing Yards" with "103/200"
- Orange progress bar (partial fill)

*Bucky Irving Sheet (Pending):*
- Full name: "BUCKY IRVING"
- Team info: "BUC • RB • #7"
- Status: "Pending: 100 Rushing Yards"
- Progress: "101/100" (over goal)
- Blue tooltip: "Yardage totals can change. Check back for final scoring"
- Info icon (i) next to stat
- Circular loading/pending icon

**Color Scheme:**
- Background: Dark navy/black
- Primary blue: #0066FF (brand color)
- Progress yellow/orange: #FF8C00
- Success green: #00C853
- Pending blue: Light blue (#64B5F6 or similar)
- Text: White/light gray

**Typography:**
- Tile names: Abbreviated (first initial + last name)
- Detail sheet: Full uppercase names
- Stats: Bold numbers with units

---

## Implementation Notes

Start with:
1. Basic 5x5 grid layout
2. Static tile components with all variants
3. State management for tile data
4. Simple state changes (no animation)
5. Add entry animations
6. Add state change animations
7. Add row/card completion logic and animations

This modular approach allows testing at each stage before adding complexity.

---

## Questions for Product/Design Team

1. ~~What should the Game Strip contain/display?~~ ✅ **ANSWERED**: Shows team logos, scores, clock, quarter
2. What are the exact visual treatments for each state? (Partially answered via screenshots)
3. Should there be sound effects with animations?
4. What happens after full card completion?
5. Color palette and brand guidelines? (Partially shown: Blue, Orange, Green, Yellow)
6. Maximum number of cards per user?
7. Mobile-first or desktop-first priority? (Appears to be mobile-first from screenshots)
8. What is the exact threshold value for pending → complete transition? (Suggested: 5-10 yards)
9. How often should real-time stat updates occur?
10. Should users be able to manually refresh stats?
11. What accessibility features are required?
12. Should there be push notifications for tile completions?

---

## Success Criteria

- ✅ All tile types display correctly with required information
- ✅ Center tile is always Lombardi trophy free spot
- ✅ Game strip shows live game data (teams, scores, clock)
- ✅ Yellow border appears on tiles with active progress
- ✅ Pending state correctly shows when goal reached but waiting for confirmation
- ✅ Threshold logic prevents premature completion (protects against negative yards)
- ✅ Complete tiles show green checkmark
- ✅ Bottom sheet opens on tile tap with full details
- ✅ Progress bars accurately reflect current vs. target stats
- ✅ Smooth, delightful animations enhance UX without slowing it down
- ✅ All state transitions are visually distinct and clear
- ✅ Real-time stat updates work correctly
- ✅ Responsive and performant on mobile and desktop
- ✅ Accessible to all users
- ✅ Code is maintainable and well-documented
