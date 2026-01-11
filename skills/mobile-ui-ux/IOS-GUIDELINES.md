# iOS Human Interface Guidelines Reference

Detailed iOS-specific design patterns following Apple's Human Interface Guidelines.

## Design Philosophy

### iOS Design Principles
1. **Clarity** - Text is legible, icons precise, adornments subtle
2. **Deference** - UI helps people understand content, never competes
3. **Depth** - Visual layers and motion convey hierarchy

### Visual Design
- Clean, uncluttered interfaces
- Edge-to-edge content
- Translucency and blur effects (vibrancy)
- Subtle gradients and shadows

## Typography

### SF Pro (System Font)
```
Font Styles:
- Large Title:    34pt, Bold
- Title 1:        28pt, Bold
- Title 2:        22pt, Bold
- Title 3:        20pt, Semibold
- Headline:       17pt, Semibold
- Body:           17pt, Regular
- Callout:        16pt, Regular
- Subheadline:    15pt, Regular
- Footnote:       13pt, Regular
- Caption 1:      12pt, Regular
- Caption 2:      11pt, Regular
```

### Dynamic Type Support
```javascript
// React Native - respect iOS text scaling
import { Text } from 'react-native';

// Use maxFontSizeMultiplier to prevent extreme scaling
<Text
  maxFontSizeMultiplier={1.5}
  allowFontScaling={true}
>
  Scalable text
</Text>
```

## Color

### System Colors
```javascript
const iOSColors = {
  // Adaptable colors (light/dark aware)
  systemBlue: '#007AFF',
  systemGreen: '#34C759',
  systemIndigo: '#5856D6',
  systemOrange: '#FF9500',
  systemPink: '#FF2D55',
  systemPurple: '#AF52DE',
  systemRed: '#FF3B30',
  systemTeal: '#5AC8FA',
  systemYellow: '#FFCC00',

  // Grayscale
  systemGray: '#8E8E93',
  systemGray2: '#AEAEB2',
  systemGray3: '#C7C7CC',
  systemGray4: '#D1D1D6',
  systemGray5: '#E5E5EA',
  systemGray6: '#F2F2F7',

  // Semantic colors
  label: '#000000',              // Primary text
  secondaryLabel: '#3C3C43',     // 60% opacity
  tertiaryLabel: '#3C3C43',      // 30% opacity
  separator: '#3C3C43',          // 29% opacity
  background: '#FFFFFF',
  secondaryBackground: '#F2F2F7',
  groupedBackground: '#F2F2F7',
};
```

## Navigation

### Navigation Bar
- Height: 44pt (standard), 96pt (large title)
- Large titles collapse on scroll
- Prefer text buttons over icons when space permits
- Back button shows previous screen title

```javascript
// React Navigation iOS styling
const screenOptions = {
  headerLargeTitle: true,
  headerTranslucent: true,
  headerBlurEffect: 'systemMaterial',
  headerStyle: {
    backgroundColor: 'transparent',
  },
};
```

### Tab Bar
- Height: 49pt (plus safe area)
- 2-5 tabs maximum
- SF Symbols for icons
- Badge support for notifications

### Sheets and Modals
```
Sheet sizes:
- .medium: ~50% screen height
- .large: ~90% screen height
- Detents: Snap points for dragging

Presentation:
- Cards lift from bottom
- Dimmed background
- Swipe down to dismiss
- Grabber handle at top (optional)
```

## Components

### Buttons
```javascript
const iOSButtonStyles = {
  // Filled button (primary)
  filled: {
    backgroundColor: '#007AFF',
    borderRadius: 14,
    paddingVertical: 14,
    paddingHorizontal: 20,
  },

  // Gray button (secondary)
  gray: {
    backgroundColor: '#E5E5EA',
    borderRadius: 14,
    paddingVertical: 14,
    paddingHorizontal: 20,
  },

  // Tinted button
  tinted: {
    backgroundColor: 'rgba(0, 122, 255, 0.15)',
    borderRadius: 14,
    paddingVertical: 14,
    paddingHorizontal: 20,
  },

  // Plain button (text only)
  plain: {
    paddingVertical: 14,
    paddingHorizontal: 20,
  },
};
```

### Lists
```
List Styles:
- Inset grouped: Rounded cards with margin
- Grouped: Full-width sections
- Plain: Simple divider lines

Row heights:
- Standard: 44pt minimum
- Subtitle: 58pt
- Custom: As needed

Swipe actions:
- Leading: Secondary actions
- Trailing: Primary/destructive actions
- Full swipe: Quick action
```

### Text Fields
```javascript
const iOSTextFieldStyle = {
  backgroundColor: '#E5E5EA',
  borderRadius: 10,
  paddingHorizontal: 16,
  paddingVertical: 12,
  fontSize: 17,
};

// Clear button
// Search icon for search fields
// Secure entry for passwords
```

### Alerts and Action Sheets
```
Alerts:
- Title (required)
- Message (optional)
- 1-3 buttons
- Destructive actions in red
- Cancel button on left or bottom

Action Sheets:
- Rise from bottom
- Cancel button separate
- Destructive options marked
- Can include message
```

## Gestures

### Standard Gestures
| Gesture | Action |
|---------|--------|
| Tap | Select, activate |
| Long press | Context menu, edit mode |
| Swipe (edge) | Back navigation |
| Swipe (content) | Reveal actions |
| Pull down | Refresh, dismiss |
| Pinch | Zoom |
| Two-finger scroll | Fast scroll |

### Context Menus
- Triggered by long press or 3D Touch
- Show preview of content
- Hierarchical menus supported
- Haptic feedback on activation

## Haptics

### Feedback Types
```javascript
import * as Haptics from 'expo-haptics';

// Impact feedback (physical collisions)
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Heavy);

// Notification feedback (outcomes)
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Warning);
Haptics.notificationAsync(Haptics.NotificationFeedbackType.Error);

// Selection feedback (UI changes)
Haptics.selectionAsync();
```

## Animations

### Standard Durations
```
Quick: 0.2s - Button presses, toggles
Standard: 0.3s - View transitions
Slow: 0.5s - Complex animations

Easing:
- easeInOut: Most UI animations
- spring: Bouncy, natural feel
- linear: Progress indicators
```

### Motion Principles
- Follow finger during gestures
- Maintain spatial relationships
- Respect reduce motion setting

```javascript
import { AccessibilityInfo } from 'react-native';

const [reduceMotion, setReduceMotion] = useState(false);

useEffect(() => {
  AccessibilityInfo.isReduceMotionEnabled().then(setReduceMotion);
}, []);

const animationDuration = reduceMotion ? 0 : 300;
```

## Safe Areas

### Insets
```
Status bar: 44pt (notch) / 20pt (classic)
Home indicator: 34pt
Navigation bar: 44pt
Tab bar: 49pt
Keyboard: Variable
```

```javascript
import { useSafeAreaInsets } from 'react-native-safe-area-context';

function Screen() {
  const insets = useSafeAreaInsets();

  return (
    <View style={{
      paddingTop: insets.top,
      paddingBottom: insets.bottom,
      paddingLeft: insets.left,
      paddingRight: insets.right,
    }}>
      {/* Content */}
    </View>
  );
}
```

## Dark Mode

### Semantic Colors
Always use semantic colors that adapt automatically:

```javascript
import { useColorScheme } from 'react-native';

function App() {
  const colorScheme = useColorScheme();

  const colors = {
    background: colorScheme === 'dark' ? '#000000' : '#FFFFFF',
    secondaryBackground: colorScheme === 'dark' ? '#1C1C1E' : '#F2F2F7',
    label: colorScheme === 'dark' ? '#FFFFFF' : '#000000',
    secondaryLabel: colorScheme === 'dark' ? '#EBEBF5' : '#3C3C43',
  };

  return (/* ... */);
}
```

### Dark Mode Adjustments
- Elevated surfaces are lighter, not darker
- Reduce shadow intensity
- Borders may become more visible
- Vibrancy effects adapt automatically

## Accessibility

### VoiceOver
- All controls need accessibilityLabel
- Use accessibilityHint for non-obvious actions
- Group related elements with accessibilityElements
- Announce dynamic changes

### Dynamic Type
- Support all sizes from xSmall to AX5
- Test at largest sizes
- Allow horizontal scrolling for extreme sizes
- Never truncate important information

### Reduce Motion
- Provide alternative transitions
- Disable parallax effects
- Use crossfade instead of slide

## SF Symbols

### Usage
- Use SF Symbols for consistency
- Match weight to adjacent text
- Support multiple sizes
- Symbols adapt to text size

```javascript
// Using expo-symbols or react-native-sf-symbols
import { SymbolView } from 'expo-symbols';

<SymbolView
  name="star.fill"
  size={24}
  tintColor="#FFD700"
  weight="semibold"
/>
```

### Naming Convention
```
Primary variant:     star
Filled variant:      star.fill
Slashed variant:     star.slash
Circle variant:      star.circle
Badge variant:       star.badge.plus
```
