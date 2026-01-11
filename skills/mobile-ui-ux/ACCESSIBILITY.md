# Mobile Accessibility Guidelines

Comprehensive accessibility checklist and implementation guide for React Native apps.

## Why Accessibility Matters

- **15%+ of users** have some form of disability
- **Legal requirements** (ADA, Section 508, WCAG)
- **Better UX for everyone** - accessibility features benefit all users
- **App Store requirements** - both platforms emphasize accessibility

## Core Principles (POUR)

1. **Perceivable** - Users can perceive content (see, hear, or feel)
2. **Operable** - Users can navigate and interact
3. **Understandable** - Content and UI are comprehensible
4. **Robust** - Works with assistive technologies

---

## Screen Reader Support

### VoiceOver (iOS) & TalkBack (Android)

#### Basic Accessibility Props
```jsx
<Pressable
  accessible={true}                    // Element is accessible
  accessibilityLabel="Add to cart"     // What screen reader announces
  accessibilityHint="Adds item to your shopping cart"  // Additional context
  accessibilityRole="button"           // Type of element
  accessibilityState={{                // Current state
    disabled: false,
    selected: false,
    checked: false,
    busy: false,
    expanded: false,
  }}
>
  <PlusIcon />
</Pressable>
```

#### Accessibility Roles
```jsx
// Use semantic roles for proper announcements
const roles = [
  'none',           // Not accessible
  'button',         // Clickable button
  'link',           // Navigational link
  'search',         // Search field
  'image',          // Image/graphic
  'text',           // Static text
  'adjustable',     // Slider, stepper
  'header',         // Section header
  'summary',        // Summary info
  'alert',          // Important message
  'checkbox',       // Toggleable checkbox
  'combobox',       // Dropdown/picker
  'menu',           // Menu container
  'menubar',        // Menu bar
  'menuitem',       // Menu item
  'progressbar',    // Progress indicator
  'radio',          // Radio button
  'radiogroup',     // Radio button group
  'scrollbar',      // Scroll indicator
  'spinbutton',     // Number input
  'switch',         // Toggle switch
  'tab',            // Tab button
  'tablist',        // Tab container
  'timer',          // Timer/countdown
  'toolbar',        // Toolbar container
];
```

#### Grouping Elements
```jsx
// Group related elements so screen reader announces them together
<View
  accessible={true}
  accessibilityLabel="Product: Blue T-Shirt, Price: $29.99, In Stock"
>
  <Text>Blue T-Shirt</Text>
  <Text>$29.99</Text>
  <Text>In Stock</Text>
</View>

// Or use accessibilityElements for custom order
<View accessibilityElements={[titleRef, priceRef, stockRef]}>
  <Text ref={stockRef}>In Stock</Text>
  <Text ref={titleRef}>Blue T-Shirt</Text>
  <Text ref={priceRef}>$29.99</Text>
</View>
```

#### Live Regions (Dynamic Content)
```jsx
// Announce changes to dynamic content
<View
  accessibilityLiveRegion="polite"  // 'none' | 'polite' | 'assertive'
  accessibilityRole="alert"
>
  <Text>{errorMessage}</Text>
</View>

// polite: Announces when user is idle
// assertive: Interrupts current announcement
```

#### Actions
```jsx
// Custom accessibility actions
<View
  accessible={true}
  accessibilityActions={[
    { name: 'increment', label: 'Increase quantity' },
    { name: 'decrement', label: 'Decrease quantity' },
    { name: 'activate', label: 'Add to cart' },
  ]}
  onAccessibilityAction={(event) => {
    switch (event.nativeEvent.actionName) {
      case 'increment':
        setQuantity(q => q + 1);
        break;
      case 'decrement':
        setQuantity(q => Math.max(0, q - 1));
        break;
      case 'activate':
        addToCart();
        break;
    }
  }}
>
  {/* Content */}
</View>
```

---

## Visual Accessibility

### Color Contrast

#### WCAG Requirements
```
Normal text (<18pt):     4.5:1 minimum contrast ratio
Large text (≥18pt bold): 3:1 minimum contrast ratio
UI components:           3:1 minimum contrast ratio

AAA (enhanced):
Normal text:             7:1 contrast ratio
Large text:              4.5:1 contrast ratio
```

#### Testing Contrast
```javascript
// Calculate contrast ratio
function getContrastRatio(foreground, background) {
  const getLuminance = (hex) => {
    const rgb = hexToRgb(hex);
    const [r, g, b] = rgb.map(c => {
      c = c / 255;
      return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
    });
    return 0.2126 * r + 0.7152 * g + 0.0722 * b;
  };

  const l1 = getLuminance(foreground);
  const l2 = getLuminance(background);
  const lighter = Math.max(l1, l2);
  const darker = Math.min(l1, l2);

  return (lighter + 0.05) / (darker + 0.05);
}

// Example
getContrastRatio('#000000', '#FFFFFF'); // 21:1 (maximum)
getContrastRatio('#767676', '#FFFFFF'); // 4.54:1 (passes AA)
```

#### Don't Rely on Color Alone
```jsx
// Bad: Only color indicates error
<TextInput
  style={{ borderColor: hasError ? 'red' : 'gray' }}
/>

// Good: Color + icon + text
<View>
  <TextInput
    style={{ borderColor: hasError ? '#FF3B30' : '#C7C7CC' }}
    accessibilityLabel={`Email${hasError ? ', error: invalid email format' : ''}`}
  />
  {hasError && (
    <View style={styles.errorContainer}>
      <ErrorIcon color="#FF3B30" />
      <Text style={styles.errorText}>Invalid email format</Text>
    </View>
  )}
</View>
```

### Dynamic Type / Font Scaling

#### Support System Font Scaling
```jsx
// Allow font scaling (default behavior)
<Text allowFontScaling={true}>
  This text scales with system settings
</Text>

// Limit extreme scaling to prevent layout breaks
<Text
  allowFontScaling={true}
  maxFontSizeMultiplier={1.5}  // Cap at 150%
>
  Limited scaling text
</Text>

// For critical UI, test at these multipliers:
// iOS: 0.82x to 3.12x (with accessibility sizes)
// Android: 0.85x to 2.0x (typical range)
```

#### Responsive Typography
```jsx
import { PixelRatio, StyleSheet } from 'react-native';

// Scale-aware sizing
const fontScale = PixelRatio.getFontScale();

const styles = StyleSheet.create({
  container: {
    // Use minHeight instead of fixed height
    minHeight: 48 * fontScale,
    paddingVertical: 12,
  },
  text: {
    fontSize: 16,
    // Line height should scale proportionally
    lineHeight: 24 * fontScale,
  },
});
```

### Reduce Motion

```jsx
import { AccessibilityInfo, useReducedMotion } from 'react-native';

// Hook for reduce motion preference
function useReduceMotion() {
  const [reduceMotion, setReduceMotion] = useState(false);

  useEffect(() => {
    AccessibilityInfo.isReduceMotionEnabled().then(setReduceMotion);

    const subscription = AccessibilityInfo.addEventListener(
      'reduceMotionChanged',
      setReduceMotion
    );

    return () => subscription.remove();
  }, []);

  return reduceMotion;
}

// Usage in animations
function AnimatedComponent() {
  const reduceMotion = useReduceMotion();

  const animatedStyle = useAnimatedStyle(() => ({
    opacity: withTiming(isVisible ? 1 : 0, {
      duration: reduceMotion ? 0 : 300,
    }),
    // Skip transform animations if reduce motion is on
    transform: reduceMotion ? [] : [{
      translateY: withSpring(isVisible ? 0 : 50),
    }],
  }));

  return <Animated.View style={animatedStyle} />;
}
```

### Bold Text Support

```jsx
// Check if bold text is enabled
const [boldTextEnabled, setBoldTextEnabled] = useState(false);

useEffect(() => {
  AccessibilityInfo.isBoldTextEnabled().then(setBoldTextEnabled);
}, []);

// Adjust font weights accordingly
const styles = StyleSheet.create({
  text: {
    fontWeight: boldTextEnabled ? '700' : '400',
  },
});
```

---

## Motor Accessibility

### Touch Targets

```jsx
// Minimum 44x44pt (iOS) / 48x48dp (Android)
const styles = StyleSheet.create({
  touchTarget: {
    minWidth: 48,
    minHeight: 48,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

// Use hitSlop for small visual elements
<Pressable
  style={styles.smallIcon}
  hitSlop={{ top: 12, bottom: 12, left: 12, right: 12 }}
  accessibilityLabel="Close"
>
  <CloseIcon size={24} />
</Pressable>
```

### Spacing Between Targets

```jsx
// Minimum 8pt between interactive elements
const styles = StyleSheet.create({
  buttonRow: {
    flexDirection: 'row',
    gap: 12, // Safe spacing
  },
  button: {
    minHeight: 48,
    paddingHorizontal: 16,
  },
});
```

### Gesture Alternatives

```jsx
// Always provide button alternatives for gestures
function SwipeableCard({ onDelete, onArchive, children }) {
  const [showActions, setShowActions] = useState(false);

  return (
    <View>
      {/* Swipe gesture for power users */}
      <SwipeableRow onDelete={onDelete} onArchive={onArchive}>
        {children}
      </SwipeableRow>

      {/* Button alternatives for accessibility */}
      <Pressable
        accessibilityLabel="More actions"
        onPress={() => setShowActions(true)}
      >
        <MoreIcon />
      </Pressable>

      {showActions && (
        <ActionSheet
          actions={[
            { label: 'Archive', onPress: onArchive },
            { label: 'Delete', onPress: onDelete, destructive: true },
          ]}
          onClose={() => setShowActions(false)}
        />
      )}
    </View>
  );
}
```

### Time Limits

```jsx
// Allow users to extend or disable time limits
function TimedAction({ timeout = 30000, onTimeout, children }) {
  const [timeLeft, setTimeLeft] = useState(timeout);
  const [paused, setPaused] = useState(false);

  // Provide option to pause/extend
  return (
    <View>
      {children}

      <View accessibilityLiveRegion="polite">
        <Text>Time remaining: {Math.ceil(timeLeft / 1000)}s</Text>
      </View>

      <Pressable
        onPress={() => setPaused(!paused)}
        accessibilityLabel={paused ? 'Resume timer' : 'Pause timer'}
      >
        <Text>{paused ? 'Resume' : 'Pause'}</Text>
      </Pressable>

      <Pressable
        onPress={() => setTimeLeft(timeout)}
        accessibilityLabel="Add more time"
      >
        <Text>Add 30 seconds</Text>
      </Pressable>
    </View>
  );
}
```

---

## Focus Management

### Focus Order

```jsx
// Control focus order with accessibilityViewIsModal
<Modal visible={isVisible}>
  <View
    accessibilityViewIsModal={true}  // Traps focus in modal
  >
    <Text accessibilityRole="header">Modal Title</Text>
    <TextInput autoFocus accessibilityLabel="Email" />
    <Pressable accessibilityLabel="Submit">
      <Text>Submit</Text>
    </Pressable>
  </View>
</Modal>
```

### Programmatic Focus

```jsx
import { AccessibilityInfo, findNodeHandle } from 'react-native';

function FocusableComponent() {
  const buttonRef = useRef(null);

  const focusButton = () => {
    const node = findNodeHandle(buttonRef.current);
    if (node) {
      AccessibilityInfo.setAccessibilityFocus(node);
    }
  };

  // Focus after navigation or state change
  useEffect(() => {
    if (showSuccess) {
      focusButton();
    }
  }, [showSuccess]);

  return (
    <Pressable
      ref={buttonRef}
      accessible={true}
      accessibilityLabel="Continue to next step"
    >
      <Text>Continue</Text>
    </Pressable>
  );
}
```

### Skip Links

```jsx
// Provide skip navigation for screen reader users
function ScreenWithSkipLink({ children }) {
  const mainContentRef = useRef(null);

  const skipToContent = () => {
    const node = findNodeHandle(mainContentRef.current);
    AccessibilityInfo.setAccessibilityFocus(node);
  };

  return (
    <View>
      {/* Skip link - first focusable element */}
      <Pressable
        onPress={skipToContent}
        accessibilityLabel="Skip to main content"
        style={styles.skipLink}
      >
        <Text>Skip to content</Text>
      </Pressable>

      {/* Navigation */}
      <Navigation />

      {/* Main content */}
      <View ref={mainContentRef} accessible={true}>
        {children}
      </View>
    </View>
  );
}
```

---

## Forms

### Labels and Instructions

```jsx
// Always associate labels with inputs
function FormField({ label, hint, error, children }) {
  return (
    <View>
      <Text
        nativeID={`${label}-label`}
        accessibilityRole="text"
      >
        {label}
      </Text>

      {hint && (
        <Text
          nativeID={`${label}-hint`}
          style={styles.hint}
        >
          {hint}
        </Text>
      )}

      {React.cloneElement(children, {
        accessibilityLabel: label,
        accessibilityLabelledBy: `${label}-label`,
        accessibilityDescribedBy: error
          ? `${label}-error`
          : hint
          ? `${label}-hint`
          : undefined,
      })}

      {error && (
        <Text
          nativeID={`${label}-error`}
          style={styles.error}
          accessibilityRole="alert"
        >
          {error}
        </Text>
      )}
    </View>
  );
}
```

### Error Handling

```jsx
// Announce errors to screen readers
function Form({ onSubmit }) {
  const [errors, setErrors] = useState({});
  const errorSummaryRef = useRef(null);

  const handleSubmit = () => {
    const newErrors = validate();
    setErrors(newErrors);

    if (Object.keys(newErrors).length > 0) {
      // Focus error summary
      const node = findNodeHandle(errorSummaryRef.current);
      AccessibilityInfo.setAccessibilityFocus(node);
    } else {
      onSubmit();
    }
  };

  return (
    <View>
      {Object.keys(errors).length > 0 && (
        <View
          ref={errorSummaryRef}
          accessible={true}
          accessibilityRole="alert"
          accessibilityLiveRegion="assertive"
        >
          <Text style={styles.errorTitle}>
            Please fix {Object.keys(errors).length} errors:
          </Text>
          {Object.entries(errors).map(([field, message]) => (
            <Text key={field}>• {message}</Text>
          ))}
        </View>
      )}

      {/* Form fields */}
    </View>
  );
}
```

---

## Testing Checklist

### Manual Testing

#### Screen Reader Testing
- [ ] Enable VoiceOver (iOS) or TalkBack (Android)
- [ ] Navigate entire app using only screen reader
- [ ] Verify all interactive elements are announced
- [ ] Verify announcements are meaningful
- [ ] Check focus order is logical
- [ ] Test all custom gestures have alternatives

#### Visual Testing
- [ ] Test with largest font size
- [ ] Test with bold text enabled
- [ ] Test with high contrast mode
- [ ] Test with color filters (color blindness)
- [ ] Test in dark mode
- [ ] Verify no content truncation at large fonts

#### Motor Testing
- [ ] Test all touch targets are ≥48dp
- [ ] Test with switch control (iOS) / Switch Access (Android)
- [ ] Verify no time-dependent interactions
- [ ] Test with reduced motion enabled

### Automated Testing

```jsx
// Using @testing-library/react-native
import { render, screen } from '@testing-library/react-native';

test('button has accessible name', () => {
  render(<Button title="Submit" onPress={jest.fn()} />);

  const button = screen.getByRole('button', { name: 'Submit' });
  expect(button).toBeTruthy();
});

test('form field has error announcement', () => {
  render(<FormField label="Email" error="Invalid email" />);

  const error = screen.getByRole('alert');
  expect(error).toHaveTextContent('Invalid email');
});

test('image has alt text', () => {
  render(<ProductImage source={uri} alt="Blue sneakers" />);

  const image = screen.getByRole('image', { name: 'Blue sneakers' });
  expect(image).toBeTruthy();
});
```

### Tools

#### iOS
- Accessibility Inspector (Xcode)
- VoiceOver (Settings > Accessibility)
- Color Filters (Settings > Accessibility > Display)

#### Android
- Accessibility Scanner app
- TalkBack (Settings > Accessibility)
- Color correction (Settings > Accessibility)

#### Cross-Platform
- axe DevTools
- React Native Testing Library
- Detox (E2E with accessibility queries)

---

## Common Mistakes

### 1. Missing Labels
```jsx
// Bad
<Pressable onPress={handleClose}>
  <CloseIcon />
</Pressable>

// Good
<Pressable
  onPress={handleClose}
  accessibilityLabel="Close dialog"
  accessibilityRole="button"
>
  <CloseIcon />
</Pressable>
```

### 2. Placeholder as Label
```jsx
// Bad - placeholder disappears when typing
<TextInput placeholder="Email" />

// Good - persistent label
<View>
  <Text nativeID="email-label">Email</Text>
  <TextInput
    placeholder="name@example.com"
    accessibilityLabelledBy="email-label"
  />
</View>
```

### 3. Inaccessible Custom Components
```jsx
// Bad - custom switch not accessible
<View onTouchEnd={toggle}>
  <View style={isOn ? styles.on : styles.off} />
</View>

// Good - proper accessibility
<Pressable
  onPress={toggle}
  accessibilityRole="switch"
  accessibilityState={{ checked: isOn }}
  accessibilityLabel="Dark mode"
>
  <View style={isOn ? styles.on : styles.off} />
</Pressable>
```

### 4. Images Without Alt Text
```jsx
// Bad
<Image source={{ uri: productImage }} />

// Good - decorative image
<Image
  source={{ uri: productImage }}
  accessible={false}
  importantForAccessibility="no"
/>

// Good - meaningful image
<Image
  source={{ uri: productImage }}
  accessible={true}
  accessibilityLabel="Red running shoes, side view"
  accessibilityRole="image"
/>
```

### 5. Non-Semantic Headings
```jsx
// Bad - visually styled but not semantic
<Text style={styles.heading}>Settings</Text>

// Good - semantic heading
<Text
  style={styles.heading}
  accessibilityRole="header"
>
  Settings
</Text>
```
