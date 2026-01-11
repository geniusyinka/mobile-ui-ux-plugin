---
name: mobile-ui-ux
description: Mobile app UI/UX design best practices for React Native, iOS, and Android. Use when building mobile interfaces, creating mobile components, designing touch interactions, implementing mobile navigation, or when the user mentions mobile app, React Native, iOS, Android, touch targets, mobile layout, bottom sheet, tab bar, or mobile accessibility.
---

# Mobile UI/UX Design Skill

Expert guidance for creating exceptional mobile user interfaces with platform-appropriate design patterns.

## Core Principles

### Touch-First Design
- **Minimum touch target**: 44x44pt (iOS) / 48x48dp (Android)
- **Recommended touch target**: 48x48pt for better accessibility
- **Touch target spacing**: Minimum 8pt between interactive elements
- **Thumb zone**: Place primary actions in bottom 1/3 of screen (easy reach)
- **Stretch zone**: Secondary actions in middle 1/3
- **Avoid zone**: Minimize critical actions in top 1/3

### Visual Hierarchy
- Use size, weight, and color to establish importance
- Primary action should be immediately obvious
- Limit to 1-2 primary actions per screen
- Use whitespace generously - mobile screens need breathing room

### Platform Consistency
- Respect platform conventions (see IOS-GUIDELINES.md and ANDROID-GUIDELINES.md)
- Use native components when possible
- Match system fonts and sizing defaults
- Honor user's accessibility settings (Dynamic Type, font scaling)

## Layout & Spacing

### Safe Areas
```
iOS: Account for notch, home indicator, status bar
Android: Account for navigation bar, status bar, display cutouts

React Native:
- Use SafeAreaView or useSafeAreaInsets()
- Never place content behind system UI without explicit design intent
```

### Spacing Scale (8pt grid)
```
xs:  4pt   - Tight spacing, related elements
sm:  8pt   - Default spacing between elements
md:  16pt  - Section spacing
lg:  24pt  - Major section breaks
xl:  32pt  - Screen-level padding
xxl: 48pt  - Hero spacing
```

### Screen Padding
- Horizontal padding: 16-20pt minimum
- Content should never touch screen edges
- Cards/containers: 12-16pt internal padding

## Typography

### Size Guidelines
```
Caption/Label:    11-12pt
Body:             14-17pt (16pt recommended)
Subheadline:      15-18pt
Headline:         17-22pt
Title:            20-28pt
Large Title:      34pt+
```

### Line Height
- Body text: 1.4-1.6x font size
- Headlines: 1.2-1.3x font size
- Minimum line height for readability: 20pt for body text

### Best Practices
- Maximum 50-75 characters per line for readability
- Use system fonts for body text (SF Pro, Roboto)
- Limit to 2-3 font weights per screen
- Support Dynamic Type / font scaling

## Navigation Patterns

### Tab Bar (Bottom Navigation)
- 3-5 items maximum
- Icons + labels for clarity
- Active state clearly differentiated
- Height: 49pt (iOS) / 56dp (Android)
- Place most important destinations here

### Stack Navigation
- Clear back button/gesture
- Descriptive titles
- Avoid deep nesting (max 3-4 levels)

### Modal Presentation
- Use for focused tasks that interrupt flow
- Always provide clear dismiss action
- Consider sheet presentation for partial modals
- Swipe-to-dismiss for convenience

### Gestures
```
Tap:           Primary selection
Long press:    Secondary actions, context menus
Swipe:         Navigation, list actions, dismiss
Pull down:     Refresh content
Pinch:         Zoom (maps, images)
```

## Common Components

### Lists
- Row height: minimum 44pt, recommended 48-72pt
- Use separators or spacing to delineate items
- Swipe actions: max 3 actions per direction
- Pull-to-refresh for dynamic content

### Bottom Sheets
- Use for contextual actions and overflow content
- Provide multiple snap points (25%, 50%, 90%)
- Always dismissible via swipe or tap outside
- Don't cover entire screen unless necessary

### Buttons
```
Primary:    Filled, high contrast, single per screen
Secondary:  Outlined or tinted
Tertiary:   Text-only, for less important actions
Destructive: Red/warning color, require confirmation

Minimum height: 44pt
Padding: 16pt horizontal, 12pt vertical
Border radius: 8-12pt (platform dependent)
```

### Forms & Input
- Label above input field
- Placeholder text is NOT a substitute for labels
- Show validation inline, below the field
- Use appropriate keyboard types (email, phone, number)
- Auto-focus first field when appropriate
- Group related fields visually

### Cards
- Consistent border radius (8-16pt)
- Subtle shadow or border for elevation
- Tap entire card for primary action
- Keep content scannable

## Loading & Feedback

### Loading States
- Skeleton screens over spinners when possible
- Show progress for operations >2 seconds
- Optimistic updates for better perceived performance
- Never block entire UI unnecessarily

### Haptic Feedback
- Use sparingly for meaningful interactions
- Success/failure confirmation
- Selection changes
- Button presses (subtle)

### Error States
- Clear, actionable error messages
- Suggest how to fix the problem
- Don't use technical jargon
- Provide retry option when applicable

## Dark Mode

- Support both light and dark modes
- Don't just invert colors - design intentionally
- Reduce contrast slightly in dark mode (avoid pure white on black)
- Test all states in both modes
- Use semantic colors that adapt automatically

## Performance Considerations

### Perceived Performance
- Show UI skeleton immediately
- Lazy load below-fold content
- Prefetch likely next screens
- Use placeholder images

### Animation Guidelines
- 60fps target (16ms per frame)
- Duration: 200-300ms for most transitions
- Use native driver for animations
- Reduce motion for accessibility setting

## Accessibility

### Visual
- Minimum contrast ratio: 4.5:1 (body), 3:1 (large text)
- Don't rely on color alone for meaning
- Support Dynamic Type / font scaling up to 200%
- Test with bold text enabled

### Screen Readers
- All interactive elements need accessible labels
- Group related content logically
- Announce dynamic content changes
- Test with VoiceOver (iOS) and TalkBack (Android)

### Motor
- Large touch targets (48pt+)
- Adequate spacing between targets
- Support for switch control
- Avoid time-limited interactions

See ACCESSIBILITY.md for comprehensive accessibility checklist.

## React Native Specific

### Component Libraries
- Consider: React Native Paper (Material), NativeBase, Tamagui, Gluestack
- Use react-native-reanimated for performant animations
- react-native-gesture-handler for native gestures

### Performance
- Use FlatList/SectionList for long lists (never ScrollView)
- Memoize expensive components
- Use useCallback/useMemo appropriately
- Profile with React DevTools and native profilers

### Styling
- StyleSheet.create for static styles
- Consider styled-components or Tamagui for dynamic theming
- Use platform-specific styles when needed:
```javascript
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    paddingTop: Platform.OS === 'ios' ? 20 : 0,
    ...Platform.select({
      ios: { shadowColor: '#000', shadowOffset: { width: 0, height: 2 } },
      android: { elevation: 4 },
    }),
  },
});
```

## Quick Reference

| Element | iOS | Android |
|---------|-----|---------|
| Touch target | 44pt | 48dp |
| Status bar | 44-59pt | 24dp |
| Nav bar | 44pt | 56dp |
| Tab bar | 49pt | 56dp |
| Corner radius | 8-14pt | 4-16dp |
| System font | SF Pro | Roboto |

## Examples

### Good: Accessible, Touch-Friendly Button
```jsx
<Pressable
  style={({ pressed }) => [
    styles.button,
    pressed && styles.buttonPressed
  ]}
  accessibilityRole="button"
  accessibilityLabel="Submit form"
  hitSlop={{ top: 10, bottom: 10, left: 10, right: 10 }}
>
  <Text style={styles.buttonText}>Submit</Text>
</Pressable>

const styles = StyleSheet.create({
  button: {
    minHeight: 48,
    paddingHorizontal: 24,
    paddingVertical: 14,
    backgroundColor: '#007AFF',
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
  },
  buttonPressed: {
    opacity: 0.8,
  },
  buttonText: {
    color: '#FFFFFF',
    fontSize: 17,
    fontWeight: '600',
  },
});
```

### Bad: Common Mistakes
```jsx
// Too small touch target
<TouchableOpacity style={{ padding: 4 }}>
  <Icon size={16} />
</TouchableOpacity>

// Missing accessibility
<View onTouchEnd={handlePress}>
  <Text>Click me</Text>
</View>

// Placeholder as label
<TextInput placeholder="Email" />
```
