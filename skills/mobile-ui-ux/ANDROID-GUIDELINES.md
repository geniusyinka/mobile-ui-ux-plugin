# Material Design 3 (Material You) Guidelines

Detailed Android-specific design patterns following Google's Material Design 3.

## Design Philosophy

### Material Design Principles
1. **Personal** - Dynamic color adapts to user's wallpaper
2. **Adaptive** - Layouts respond to different screen sizes
3. **Expressive** - Motion and shape convey meaning

### Key Changes in M3
- Dynamic color theming
- Updated component shapes
- New typography scale
- Enhanced motion system
- Improved accessibility

## Color System

### Dynamic Color
```javascript
// Material You derives colors from user's wallpaper
// Provide fallback static theme for older Android versions

const dynamicColors = {
  // Primary colors (from wallpaper)
  primary: '#6750A4',
  onPrimary: '#FFFFFF',
  primaryContainer: '#EADDFF',
  onPrimaryContainer: '#21005D',

  // Secondary colors
  secondary: '#625B71',
  onSecondary: '#FFFFFF',
  secondaryContainer: '#E8DEF8',
  onSecondaryContainer: '#1D192B',

  // Tertiary colors
  tertiary: '#7D5260',
  onTertiary: '#FFFFFF',
  tertiaryContainer: '#FFD8E4',
  onTertiaryContainer: '#31111D',

  // Error colors
  error: '#B3261E',
  onError: '#FFFFFF',
  errorContainer: '#F9DEDC',
  onErrorContainer: '#410E0B',

  // Surface colors
  surface: '#FFFBFE',
  onSurface: '#1C1B1F',
  surfaceVariant: '#E7E0EC',
  onSurfaceVariant: '#49454F',
  outline: '#79747E',
  outlineVariant: '#CAC4D0',

  // Background
  background: '#FFFBFE',
  onBackground: '#1C1B1F',
};
```

### Surface Tones
```
Surface levels with tint overlay:
- Surface: 0% tint
- Surface 1: 5% tint (cards, sheets)
- Surface 2: 8% tint (navigation)
- Surface 3: 11% tint (search bars)
- Surface 4: 12% tint (menus)
- Surface 5: 14% tint (highest elevation)
```

## Typography

### Type Scale (M3)
```
Display Large:   57sp / -0.25 letter-spacing
Display Medium:  45sp / 0 letter-spacing
Display Small:   36sp / 0 letter-spacing
Headline Large:  32sp / 0 letter-spacing
Headline Medium: 28sp / 0 letter-spacing
Headline Small:  24sp / 0 letter-spacing
Title Large:     22sp / 0 letter-spacing
Title Medium:    16sp / 0.15 letter-spacing
Title Small:     14sp / 0.1 letter-spacing
Body Large:      16sp / 0.5 letter-spacing
Body Medium:     14sp / 0.25 letter-spacing
Body Small:      12sp / 0.4 letter-spacing
Label Large:     14sp / 0.1 letter-spacing
Label Medium:    12sp / 0.5 letter-spacing
Label Small:     11sp / 0.5 letter-spacing
```

### Font Weights
```javascript
const typeface = {
  displayLarge: { fontSize: 57, fontWeight: '400', lineHeight: 64 },
  displayMedium: { fontSize: 45, fontWeight: '400', lineHeight: 52 },
  displaySmall: { fontSize: 36, fontWeight: '400', lineHeight: 44 },
  headlineLarge: { fontSize: 32, fontWeight: '400', lineHeight: 40 },
  headlineMedium: { fontSize: 28, fontWeight: '400', lineHeight: 36 },
  headlineSmall: { fontSize: 24, fontWeight: '400', lineHeight: 32 },
  titleLarge: { fontSize: 22, fontWeight: '400', lineHeight: 28 },
  titleMedium: { fontSize: 16, fontWeight: '500', lineHeight: 24 },
  titleSmall: { fontSize: 14, fontWeight: '500', lineHeight: 20 },
  bodyLarge: { fontSize: 16, fontWeight: '400', lineHeight: 24 },
  bodyMedium: { fontSize: 14, fontWeight: '400', lineHeight: 20 },
  bodySmall: { fontSize: 12, fontWeight: '400', lineHeight: 16 },
  labelLarge: { fontSize: 14, fontWeight: '500', lineHeight: 20 },
  labelMedium: { fontSize: 12, fontWeight: '500', lineHeight: 16 },
  labelSmall: { fontSize: 11, fontWeight: '500', lineHeight: 16 },
};
```

## Shape

### Corner Radius Scale
```javascript
const shapes = {
  none: 0,
  extraSmall: 4,    // Chips, small buttons
  small: 8,         // Cards, text fields
  medium: 12,       // Dialogs, menus
  large: 16,        // FAB, bottom sheets
  extraLarge: 28,   // Large FAB
  full: 9999,       // Circular elements
};
```

### Shape Applications
```
Buttons:        Full rounded (pill shape) or 20dp
Cards:          12dp
Dialogs:        28dp
FAB:            16dp (small), 28dp (large)
Chips:          8dp
Text fields:    4dp top, 0dp bottom (filled)
Bottom sheets:  28dp top corners
```

## Navigation

### Navigation Bar (Bottom)
```javascript
const navigationBarStyle = {
  height: 80, // 80dp with labels, 56dp without
  backgroundColor: 'surface',
  elevation: 3,
  // Active indicator: pill shape behind icon
  activeIndicator: {
    backgroundColor: 'secondaryContainer',
    borderRadius: 16,
    height: 32,
    width: 64,
  },
};

// 3-5 destinations
// Icons required, labels recommended
// Badge support for notifications
```

### Navigation Rail (Tablet)
```
Width: 80dp
Use on larger screens (600dp+)
Same destinations as bottom nav
FAB can be integrated at top
```

### Navigation Drawer
```
Width: 360dp max
Permanent on large screens (1240dp+)
Modal on smaller screens
Section headers supported
Nested destinations (2 levels max)
```

### Top App Bar
```javascript
const topAppBarVariants = {
  // Center-aligned (default)
  centerAligned: {
    height: 64,
    titleAlign: 'center',
  },

  // Small (left-aligned title)
  small: {
    height: 64,
    titleAlign: 'left',
  },

  // Medium (larger title)
  medium: {
    height: 112,
    collapsedHeight: 64,
  },

  // Large (prominent title)
  large: {
    height: 152,
    collapsedHeight: 64,
  },
};
```

## Components

### Buttons
```javascript
const buttonStyles = {
  // Filled button (highest emphasis)
  filled: {
    backgroundColor: 'primary',
    color: 'onPrimary',
    borderRadius: 20,
    height: 40,
    paddingHorizontal: 24,
  },

  // Filled tonal (medium emphasis)
  filledTonal: {
    backgroundColor: 'secondaryContainer',
    color: 'onSecondaryContainer',
    borderRadius: 20,
    height: 40,
    paddingHorizontal: 24,
  },

  // Outlined (medium emphasis)
  outlined: {
    backgroundColor: 'transparent',
    borderColor: 'outline',
    borderWidth: 1,
    color: 'primary',
    borderRadius: 20,
    height: 40,
    paddingHorizontal: 24,
  },

  // Text button (lowest emphasis)
  text: {
    backgroundColor: 'transparent',
    color: 'primary',
    height: 40,
    paddingHorizontal: 12,
  },

  // Elevated button
  elevated: {
    backgroundColor: 'surfaceContainerLow',
    color: 'primary',
    elevation: 1,
    borderRadius: 20,
    height: 40,
    paddingHorizontal: 24,
  },
};
```

### FAB (Floating Action Button)
```javascript
const fabStyles = {
  // Standard FAB
  standard: {
    size: 56,
    borderRadius: 16,
    elevation: 3,
    backgroundColor: 'primaryContainer',
    iconColor: 'onPrimaryContainer',
  },

  // Small FAB
  small: {
    size: 40,
    borderRadius: 12,
    elevation: 3,
  },

  // Large FAB
  large: {
    size: 96,
    borderRadius: 28,
    elevation: 3,
  },

  // Extended FAB (with label)
  extended: {
    height: 56,
    borderRadius: 16,
    paddingHorizontal: 16,
    // Icon optional, label required
  },
};

// Position: 16dp from edges
// Can expand/collapse on scroll
```

### Cards
```javascript
const cardStyles = {
  // Elevated card
  elevated: {
    backgroundColor: 'surfaceContainerLow',
    elevation: 1,
    borderRadius: 12,
  },

  // Filled card
  filled: {
    backgroundColor: 'surfaceContainerHighest',
    elevation: 0,
    borderRadius: 12,
  },

  // Outlined card
  outlined: {
    backgroundColor: 'surface',
    borderColor: 'outlineVariant',
    borderWidth: 1,
    elevation: 0,
    borderRadius: 12,
  },
};

// Internal padding: 16dp
// Action area: Full card or specific buttons
```

### Text Fields
```javascript
const textFieldStyles = {
  // Filled text field
  filled: {
    backgroundColor: 'surfaceContainerHighest',
    borderTopLeftRadius: 4,
    borderTopRightRadius: 4,
    borderBottomWidth: 1,
    borderBottomColor: 'onSurfaceVariant',
    height: 56,
    paddingHorizontal: 16,
  },

  // Outlined text field
  outlined: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: 'outline',
    borderRadius: 4,
    height: 56,
    paddingHorizontal: 16,
  },
};

// Label: Animated from placeholder to top
// Supporting text: Below field
// Error state: Red indicator line and text
// Character counter: Optional
```

### Chips
```javascript
const chipStyles = {
  // Assist chip (smart actions)
  assist: {
    height: 32,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: 'outline',
    paddingHorizontal: 16,
  },

  // Filter chip (toggle selection)
  filter: {
    height: 32,
    borderRadius: 8,
    // Selected: filled background
    // Unselected: outline only
  },

  // Input chip (user input, removable)
  input: {
    height: 32,
    borderRadius: 8,
    // Trailing remove icon
  },

  // Suggestion chip (dynamic suggestions)
  suggestion: {
    height: 32,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: 'outline',
  },
};
```

### Bottom Sheets
```javascript
const bottomSheetStyles = {
  // Standard bottom sheet
  standard: {
    borderTopLeftRadius: 28,
    borderTopRightRadius: 28,
    backgroundColor: 'surfaceContainerLow',
    // Optional drag handle
    dragHandle: {
      width: 32,
      height: 4,
      borderRadius: 2,
      backgroundColor: 'onSurfaceVariant',
      marginTop: 22,
    },
  },

  // Modal bottom sheet
  modal: {
    // Scrim behind
    scrimColor: 'rgba(0, 0, 0, 0.32)',
    elevation: 1,
  },
};

// Expand to max 90% screen height
// Support multiple detents
```

### Dialogs
```javascript
const dialogStyle = {
  borderRadius: 28,
  backgroundColor: 'surfaceContainerHigh',
  minWidth: 280,
  maxWidth: 560,
  padding: 24,
  // Icon: Optional, centered
  // Title: Optional
  // Content: Required
  // Actions: Right-aligned, text buttons
};
```

## Motion

### Duration Tokens
```javascript
const durations = {
  short1: 50,   // Micro interactions
  short2: 100,  // Simple state changes
  short3: 150,  // Button feedback
  short4: 200,  // Chip selection
  medium1: 250, // Card expansion
  medium2: 300, // Modal entry
  medium3: 350, // Complex transitions
  medium4: 400, // Large element moves
  long1: 450,   // Page transitions
  long2: 500,   // Elaborate animations
  long3: 550,   // Emphasized entry
  long4: 600,   // Dramatic effects
};
```

### Easing Curves
```javascript
const easing = {
  // Standard easing (most UI)
  standard: 'cubic-bezier(0.2, 0, 0, 1)',

  // Emphasized easing (attention)
  emphasized: 'cubic-bezier(0.2, 0, 0, 1)',
  emphasizedDecelerate: 'cubic-bezier(0.05, 0.7, 0.1, 1)',
  emphasizedAccelerate: 'cubic-bezier(0.3, 0, 0.8, 0.15)',

  // Standard decelerate (entering)
  standardDecelerate: 'cubic-bezier(0, 0, 0, 1)',

  // Standard accelerate (exiting)
  standardAccelerate: 'cubic-bezier(0.3, 0, 1, 1)',
};
```

### Transitions
```
Enter: Fade in + scale up (0.85 → 1.0)
Exit: Fade out + scale down (1.0 → 0.85)
Shared element: Morph between states
Container transform: Expand from trigger
```

## Elevation

### Elevation Levels
```javascript
const elevation = {
  level0: 0,   // Surface
  level1: 1,   // Cards, buttons
  level2: 3,   // Navigation bar
  level3: 6,   // FAB, snackbar
  level4: 8,   // Menus, side sheets
  level5: 12,  // Dialogs, modals
};

// M3 uses tonal elevation (surface tint) instead of shadows
// Shadows still exist but are more subtle
```

## Accessibility

### Touch Targets
- Minimum: 48dp x 48dp
- Recommended: 56dp for primary actions
- Spacing between targets: 8dp minimum

### Contrast
- Normal text: 4.5:1 minimum
- Large text (18sp+): 3:1 minimum
- Non-text elements: 3:1 minimum

### TalkBack Support
```javascript
// React Native accessibility props
<Pressable
  accessible={true}
  accessibilityLabel="Add to cart"
  accessibilityHint="Adds this item to your shopping cart"
  accessibilityRole="button"
  accessibilityState={{ disabled: false }}
>
  <Text>Add</Text>
</Pressable>
```

## Responsive Layout

### Breakpoints
```
Compact:   0-599dp (phone portrait)
Medium:    600-839dp (tablet portrait, foldable)
Expanded:  840dp+ (tablet landscape, desktop)
```

### Adaptive Layouts
```
Compact:
- Single column
- Bottom navigation
- Full-width components

Medium:
- Navigation rail
- Two-column possible
- Cards in grid

Expanded:
- Navigation drawer (permanent)
- Multi-column layouts
- Side panels
```

### Window Size Classes
```javascript
import { useWindowDimensions } from 'react-native';

function useWindowSizeClass() {
  const { width } = useWindowDimensions();

  if (width < 600) return 'compact';
  if (width < 840) return 'medium';
  return 'expanded';
}
```

## Dark Theme

### Color Mapping
```
Light → Dark transformations:
- Primary: Lightened slightly
- Containers: Darkened, tinted with primary
- Surface: Near black with tint
- On-colors: Inverted

Elevation in dark theme:
- Uses lighter surface colors instead of shadows
- Each elevation level adds primary tint
```

### Best Practices
- Avoid pure black (#000000), use #121212 or tinted darks
- Reduce surface contrast
- Maintain color relationships
- Test all states in dark theme
