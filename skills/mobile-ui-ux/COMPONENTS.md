# Mobile Component Patterns

Production-ready patterns for common mobile UI components in React Native.

## Navigation Components

### Tab Bar
```jsx
import { View, Text, Pressable, StyleSheet } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
} from 'react-native-reanimated';

function TabBar({ tabs, activeTab, onTabPress }) {
  const insets = useSafeAreaInsets();

  return (
    <View style={[styles.tabBar, { paddingBottom: insets.bottom }]}>
      {tabs.map((tab, index) => (
        <TabItem
          key={tab.key}
          tab={tab}
          isActive={activeTab === index}
          onPress={() => onTabPress(index)}
        />
      ))}
    </View>
  );
}

function TabItem({ tab, isActive, onPress }) {
  const scale = useSharedValue(1);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
  }));

  const handlePressIn = () => {
    scale.value = withSpring(0.95);
  };

  const handlePressOut = () => {
    scale.value = withSpring(1);
  };

  return (
    <Pressable
      style={styles.tabItem}
      onPress={onPress}
      onPressIn={handlePressIn}
      onPressOut={handlePressOut}
      accessibilityRole="tab"
      accessibilityState={{ selected: isActive }}
      accessibilityLabel={tab.label}
    >
      <Animated.View style={animatedStyle}>
        <tab.icon
          size={24}
          color={isActive ? '#007AFF' : '#8E8E93'}
        />
        <Text style={[
          styles.tabLabel,
          isActive && styles.tabLabelActive
        ]}>
          {tab.label}
        </Text>
      </Animated.View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  tabBar: {
    flexDirection: 'row',
    backgroundColor: '#FFFFFF',
    borderTopWidth: StyleSheet.hairlineWidth,
    borderTopColor: '#C7C7CC',
    paddingTop: 8,
  },
  tabItem: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    minHeight: 49,
  },
  tabLabel: {
    fontSize: 10,
    marginTop: 4,
    color: '#8E8E93',
  },
  tabLabelActive: {
    color: '#007AFF',
  },
});
```

### Header with Large Title
```jsx
import Animated, {
  useAnimatedScrollHandler,
  useSharedValue,
  useAnimatedStyle,
  interpolate,
  Extrapolation,
} from 'react-native-reanimated';

function LargeTitleHeader({ title, children }) {
  const scrollY = useSharedValue(0);

  const scrollHandler = useAnimatedScrollHandler({
    onScroll: (event) => {
      scrollY.value = event.contentOffset.y;
    },
  });

  const largeTitleStyle = useAnimatedStyle(() => ({
    opacity: interpolate(
      scrollY.value,
      [0, 50],
      [1, 0],
      Extrapolation.CLAMP
    ),
    transform: [{
      translateY: interpolate(
        scrollY.value,
        [0, 100],
        [0, -50],
        Extrapolation.CLAMP
      ),
    }],
  }));

  const smallTitleStyle = useAnimatedStyle(() => ({
    opacity: interpolate(
      scrollY.value,
      [30, 60],
      [0, 1],
      Extrapolation.CLAMP
    ),
  }));

  return (
    <View style={styles.container}>
      {/* Compact header */}
      <View style={styles.compactHeader}>
        <Animated.Text style={[styles.smallTitle, smallTitleStyle]}>
          {title}
        </Animated.Text>
      </View>

      <Animated.ScrollView
        onScroll={scrollHandler}
        scrollEventThrottle={16}
        contentContainerStyle={styles.scrollContent}
      >
        {/* Large title */}
        <Animated.Text style={[styles.largeTitle, largeTitleStyle]}>
          {title}
        </Animated.Text>

        {children}
      </Animated.ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  compactHeader: {
    height: 44,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F8F8F8',
    borderBottomWidth: StyleSheet.hairlineWidth,
    borderBottomColor: '#C7C7CC',
  },
  smallTitle: {
    fontSize: 17,
    fontWeight: '600',
  },
  scrollContent: {
    paddingHorizontal: 16,
  },
  largeTitle: {
    fontSize: 34,
    fontWeight: '700',
    marginTop: 8,
    marginBottom: 16,
  },
});
```

## Lists

### Swipeable List Item
```jsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  runOnJS,
} from 'react-native-reanimated';

const SWIPE_THRESHOLD = 80;

function SwipeableListItem({ onDelete, onArchive, children }) {
  const translateX = useSharedValue(0);

  const panGesture = Gesture.Pan()
    .onUpdate((event) => {
      translateX.value = Math.max(-160, Math.min(80, event.translationX));
    })
    .onEnd((event) => {
      if (translateX.value < -SWIPE_THRESHOLD) {
        // Reveal delete action
        translateX.value = withSpring(-80);
      } else if (translateX.value > SWIPE_THRESHOLD) {
        // Reveal archive action
        translateX.value = withSpring(80);
      } else {
        translateX.value = withSpring(0);
      }
    });

  const contentStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }],
  }));

  const deleteStyle = useAnimatedStyle(() => ({
    opacity: interpolate(translateX.value, [-80, 0], [1, 0]),
  }));

  return (
    <View style={styles.container}>
      {/* Background actions */}
      <View style={styles.actionsContainer}>
        <Animated.View style={[styles.archiveAction, archiveStyle]}>
          <ArchiveIcon color="#FFFFFF" />
        </Animated.View>
        <Animated.View style={[styles.deleteAction, deleteStyle]}>
          <TrashIcon color="#FFFFFF" />
        </Animated.View>
      </View>

      {/* Swipeable content */}
      <GestureDetector gesture={panGesture}>
        <Animated.View style={[styles.content, contentStyle]}>
          {children}
        </Animated.View>
      </GestureDetector>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    overflow: 'hidden',
  },
  actionsContainer: {
    ...StyleSheet.absoluteFillObject,
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  archiveAction: {
    width: 80,
    backgroundColor: '#34C759',
    justifyContent: 'center',
    alignItems: 'center',
  },
  deleteAction: {
    width: 80,
    backgroundColor: '#FF3B30',
    justifyContent: 'center',
    alignItems: 'center',
  },
  content: {
    backgroundColor: '#FFFFFF',
  },
});
```

### Pull to Refresh
```jsx
import { RefreshControl, FlatList } from 'react-native';
import { useState, useCallback } from 'react';

function PullToRefreshList({ data, renderItem, onRefresh }) {
  const [refreshing, setRefreshing] = useState(false);

  const handleRefresh = useCallback(async () => {
    setRefreshing(true);
    try {
      await onRefresh();
    } finally {
      setRefreshing(false);
    }
  }, [onRefresh]);

  return (
    <FlatList
      data={data}
      renderItem={renderItem}
      keyExtractor={(item) => item.id}
      refreshControl={
        <RefreshControl
          refreshing={refreshing}
          onRefresh={handleRefresh}
          tintColor="#007AFF"
          title="Pull to refresh"
          titleColor="#8E8E93"
        />
      }
      contentContainerStyle={styles.listContent}
    />
  );
}
```

## Bottom Sheet

### Gesture-Driven Bottom Sheet
```jsx
import { Dimensions, View, StyleSheet } from 'react-native';
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  interpolate,
  runOnJS,
} from 'react-native-reanimated';

const { height: SCREEN_HEIGHT } = Dimensions.get('window');

const SNAP_POINTS = {
  collapsed: SCREEN_HEIGHT - 100,
  half: SCREEN_HEIGHT * 0.5,
  expanded: SCREEN_HEIGHT * 0.1,
};

function BottomSheet({ children, onClose }) {
  const translateY = useSharedValue(SNAP_POINTS.collapsed);
  const context = useSharedValue({ y: 0 });

  const panGesture = Gesture.Pan()
    .onStart(() => {
      context.value = { y: translateY.value };
    })
    .onUpdate((event) => {
      translateY.value = Math.max(
        SNAP_POINTS.expanded,
        context.value.y + event.translationY
      );
    })
    .onEnd((event) => {
      // Snap to nearest point
      const snapPoints = Object.values(SNAP_POINTS);
      const distances = snapPoints.map((point) =>
        Math.abs(translateY.value - point)
      );
      const nearestIndex = distances.indexOf(Math.min(...distances));

      // Close if dragged past collapsed
      if (translateY.value > SNAP_POINTS.collapsed + 50) {
        runOnJS(onClose)();
        return;
      }

      translateY.value = withSpring(snapPoints[nearestIndex], {
        damping: 20,
        stiffness: 200,
      });
    });

  const sheetStyle = useAnimatedStyle(() => ({
    transform: [{ translateY: translateY.value }],
  }));

  const backdropStyle = useAnimatedStyle(() => ({
    opacity: interpolate(
      translateY.value,
      [SNAP_POINTS.expanded, SNAP_POINTS.collapsed],
      [0.5, 0]
    ),
  }));

  return (
    <>
      {/* Backdrop */}
      <Animated.View
        style={[styles.backdrop, backdropStyle]}
        pointerEvents="none"
      />

      {/* Sheet */}
      <GestureDetector gesture={panGesture}>
        <Animated.View style={[styles.sheet, sheetStyle]}>
          {/* Handle */}
          <View style={styles.handleContainer}>
            <View style={styles.handle} />
          </View>

          {/* Content */}
          <View style={styles.content}>
            {children}
          </View>
        </Animated.View>
      </GestureDetector>
    </>
  );
}

const styles = StyleSheet.create({
  backdrop: {
    ...StyleSheet.absoluteFillObject,
    backgroundColor: '#000000',
  },
  sheet: {
    position: 'absolute',
    left: 0,
    right: 0,
    height: SCREEN_HEIGHT,
    backgroundColor: '#FFFFFF',
    borderTopLeftRadius: 20,
    borderTopRightRadius: 20,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: -4 },
    shadowOpacity: 0.1,
    shadowRadius: 8,
    elevation: 16,
  },
  handleContainer: {
    alignItems: 'center',
    paddingVertical: 12,
  },
  handle: {
    width: 36,
    height: 5,
    backgroundColor: '#D1D1D6',
    borderRadius: 3,
  },
  content: {
    flex: 1,
    paddingHorizontal: 16,
  },
});
```

## Forms

### Floating Label Input
```jsx
import { useState, useRef } from 'react';
import { TextInput, View, Text, StyleSheet, Pressable } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withTiming,
  interpolate,
  interpolateColor,
} from 'react-native-reanimated';

function FloatingLabelInput({
  label,
  value,
  onChangeText,
  error,
  secureTextEntry,
  keyboardType,
  autoCapitalize,
  ...props
}) {
  const inputRef = useRef(null);
  const [isFocused, setIsFocused] = useState(false);
  const animation = useSharedValue(value ? 1 : 0);

  const handleFocus = () => {
    setIsFocused(true);
    animation.value = withTiming(1, { duration: 150 });
  };

  const handleBlur = () => {
    setIsFocused(false);
    if (!value) {
      animation.value = withTiming(0, { duration: 150 });
    }
  };

  const labelStyle = useAnimatedStyle(() => ({
    transform: [
      {
        translateY: interpolate(animation.value, [0, 1], [0, -24]),
      },
      {
        scale: interpolate(animation.value, [0, 1], [1, 0.85]),
      },
    ],
    color: interpolateColor(
      animation.value,
      [0, 1],
      ['#8E8E93', error ? '#FF3B30' : '#007AFF']
    ),
  }));

  const containerStyle = useAnimatedStyle(() => ({
    borderColor: interpolateColor(
      animation.value,
      [0, 1],
      ['#C7C7CC', error ? '#FF3B30' : '#007AFF']
    ),
  }));

  return (
    <View style={styles.wrapper}>
      <Pressable onPress={() => inputRef.current?.focus()}>
        <Animated.View style={[styles.container, containerStyle]}>
          <Animated.Text style={[styles.label, labelStyle]}>
            {label}
          </Animated.Text>

          <TextInput
            ref={inputRef}
            style={styles.input}
            value={value}
            onChangeText={onChangeText}
            onFocus={handleFocus}
            onBlur={handleBlur}
            secureTextEntry={secureTextEntry}
            keyboardType={keyboardType}
            autoCapitalize={autoCapitalize}
            accessibilityLabel={label}
            {...props}
          />
        </Animated.View>
      </Pressable>

      {error && (
        <Text style={styles.error} accessibilityRole="alert">
          {error}
        </Text>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  wrapper: {
    marginBottom: 16,
  },
  container: {
    height: 56,
    borderWidth: 1,
    borderRadius: 8,
    paddingHorizontal: 16,
    justifyContent: 'center',
    backgroundColor: '#FFFFFF',
  },
  label: {
    position: 'absolute',
    left: 16,
    fontSize: 16,
    backgroundColor: '#FFFFFF',
    paddingHorizontal: 4,
  },
  input: {
    fontSize: 16,
    color: '#000000',
    paddingTop: 8,
  },
  error: {
    color: '#FF3B30',
    fontSize: 12,
    marginTop: 4,
    marginLeft: 16,
  },
});
```

## Buttons

### Pressable Button with Haptics
```jsx
import { Pressable, Text, StyleSheet, ActivityIndicator } from 'react-native';
import * as Haptics from 'expo-haptics';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
} from 'react-native-reanimated';

function Button({
  title,
  onPress,
  variant = 'primary',
  size = 'medium',
  loading = false,
  disabled = false,
  icon,
}) {
  const scale = useSharedValue(1);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
  }));

  const handlePressIn = () => {
    scale.value = withSpring(0.97);
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
  };

  const handlePressOut = () => {
    scale.value = withSpring(1);
  };

  const handlePress = () => {
    if (!loading && !disabled) {
      onPress?.();
    }
  };

  const buttonStyles = [
    styles.button,
    styles[variant],
    styles[size],
    disabled && styles.disabled,
  ];

  const textStyles = [
    styles.text,
    styles[`${variant}Text`],
    styles[`${size}Text`],
  ];

  return (
    <Pressable
      onPress={handlePress}
      onPressIn={handlePressIn}
      onPressOut={handlePressOut}
      disabled={disabled || loading}
      accessibilityRole="button"
      accessibilityState={{ disabled: disabled || loading }}
      accessibilityLabel={title}
    >
      <Animated.View style={[buttonStyles, animatedStyle]}>
        {loading ? (
          <ActivityIndicator
            color={variant === 'primary' ? '#FFFFFF' : '#007AFF'}
          />
        ) : (
          <>
            {icon && <View style={styles.iconContainer}>{icon}</View>}
            <Text style={textStyles}>{title}</Text>
          </>
        )}
      </Animated.View>
    </Pressable>
  );
}

const styles = StyleSheet.create({
  button: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
    borderRadius: 12,
  },

  // Variants
  primary: {
    backgroundColor: '#007AFF',
  },
  secondary: {
    backgroundColor: '#E5E5EA',
  },
  outline: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: '#007AFF',
  },
  text: {
    backgroundColor: 'transparent',
  },
  destructive: {
    backgroundColor: '#FF3B30',
  },

  // Sizes
  small: {
    height: 36,
    paddingHorizontal: 12,
  },
  medium: {
    height: 48,
    paddingHorizontal: 20,
  },
  large: {
    height: 56,
    paddingHorizontal: 24,
  },

  // Text styles
  text: {
    fontWeight: '600',
  },
  primaryText: {
    color: '#FFFFFF',
  },
  secondaryText: {
    color: '#007AFF',
  },
  outlineText: {
    color: '#007AFF',
  },
  textText: {
    color: '#007AFF',
  },
  destructiveText: {
    color: '#FFFFFF',
  },
  smallText: {
    fontSize: 14,
  },
  mediumText: {
    fontSize: 16,
  },
  largeText: {
    fontSize: 18,
  },

  // States
  disabled: {
    opacity: 0.5,
  },

  iconContainer: {
    marginRight: 8,
  },
});
```

## Modals

### Action Sheet
```jsx
import { View, Text, Pressable, StyleSheet, Modal } from 'react-native';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
  runOnJS,
} from 'react-native-reanimated';
import { useEffect } from 'react';

function ActionSheet({ visible, onClose, title, message, actions }) {
  const insets = useSafeAreaInsets();
  const translateY = useSharedValue(300);
  const opacity = useSharedValue(0);

  useEffect(() => {
    if (visible) {
      opacity.value = withTiming(1, { duration: 200 });
      translateY.value = withSpring(0, { damping: 20 });
    } else {
      opacity.value = withTiming(0, { duration: 150 });
      translateY.value = withTiming(300, { duration: 200 });
    }
  }, [visible]);

  const backdropStyle = useAnimatedStyle(() => ({
    opacity: opacity.value,
  }));

  const sheetStyle = useAnimatedStyle(() => ({
    transform: [{ translateY: translateY.value }],
  }));

  const handleClose = () => {
    opacity.value = withTiming(0, { duration: 150 });
    translateY.value = withTiming(300, { duration: 200 }, () => {
      runOnJS(onClose)();
    });
  };

  return (
    <Modal
      visible={visible}
      transparent
      animationType="none"
      onRequestClose={handleClose}
    >
      <View style={styles.container}>
        {/* Backdrop */}
        <Pressable style={StyleSheet.absoluteFill} onPress={handleClose}>
          <Animated.View style={[styles.backdrop, backdropStyle]} />
        </Pressable>

        {/* Sheet */}
        <Animated.View
          style={[
            styles.sheet,
            sheetStyle,
            { paddingBottom: insets.bottom + 8 },
          ]}
        >
          {/* Header */}
          {(title || message) && (
            <View style={styles.header}>
              {title && <Text style={styles.title}>{title}</Text>}
              {message && <Text style={styles.message}>{message}</Text>}
            </View>
          )}

          {/* Actions */}
          <View style={styles.actionsContainer}>
            {actions.map((action, index) => (
              <Pressable
                key={index}
                style={({ pressed }) => [
                  styles.action,
                  pressed && styles.actionPressed,
                  index > 0 && styles.actionBorder,
                ]}
                onPress={() => {
                  action.onPress?.();
                  handleClose();
                }}
                accessibilityRole="button"
              >
                <Text
                  style={[
                    styles.actionText,
                    action.destructive && styles.destructiveText,
                  ]}
                >
                  {action.label}
                </Text>
              </Pressable>
            ))}
          </View>

          {/* Cancel */}
          <Pressable
            style={({ pressed }) => [
              styles.cancelButton,
              pressed && styles.actionPressed,
            ]}
            onPress={handleClose}
            accessibilityRole="button"
          >
            <Text style={styles.cancelText}>Cancel</Text>
          </Pressable>
        </Animated.View>
      </View>
    </Modal>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'flex-end',
  },
  backdrop: {
    ...StyleSheet.absoluteFillObject,
    backgroundColor: 'rgba(0, 0, 0, 0.4)',
  },
  sheet: {
    paddingHorizontal: 8,
  },
  header: {
    backgroundColor: '#F2F2F7',
    borderTopLeftRadius: 14,
    borderTopRightRadius: 14,
    paddingVertical: 16,
    paddingHorizontal: 16,
    alignItems: 'center',
  },
  title: {
    fontSize: 13,
    fontWeight: '600',
    color: '#8E8E93',
    textAlign: 'center',
  },
  message: {
    fontSize: 13,
    color: '#8E8E93',
    textAlign: 'center',
    marginTop: 4,
  },
  actionsContainer: {
    backgroundColor: '#F2F2F7',
    borderBottomLeftRadius: 14,
    borderBottomRightRadius: 14,
    overflow: 'hidden',
  },
  action: {
    paddingVertical: 18,
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
  },
  actionPressed: {
    backgroundColor: '#E5E5EA',
  },
  actionBorder: {
    borderTopWidth: StyleSheet.hairlineWidth,
    borderTopColor: '#C7C7CC',
  },
  actionText: {
    fontSize: 20,
    color: '#007AFF',
  },
  destructiveText: {
    color: '#FF3B30',
  },
  cancelButton: {
    marginTop: 8,
    paddingVertical: 18,
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
    borderRadius: 14,
  },
  cancelText: {
    fontSize: 20,
    fontWeight: '600',
    color: '#007AFF',
  },
});
```

## Loading States

### Skeleton Loader
```jsx
import { View, StyleSheet } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withRepeat,
  withTiming,
  interpolate,
} from 'react-native-reanimated';
import { useEffect } from 'react';

function Skeleton({ width, height, borderRadius = 4 }) {
  const animation = useSharedValue(0);

  useEffect(() => {
    animation.value = withRepeat(
      withTiming(1, { duration: 1000 }),
      -1,
      true
    );
  }, []);

  const animatedStyle = useAnimatedStyle(() => ({
    opacity: interpolate(animation.value, [0, 1], [0.3, 0.7]),
  }));

  return (
    <Animated.View
      style={[
        styles.skeleton,
        { width, height, borderRadius },
        animatedStyle,
      ]}
    />
  );
}

function CardSkeleton() {
  return (
    <View style={styles.card}>
      <Skeleton width={60} height={60} borderRadius={30} />
      <View style={styles.cardContent}>
        <Skeleton width="80%" height={16} />
        <Skeleton width="60%" height={14} />
        <Skeleton width="40%" height={14} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  skeleton: {
    backgroundColor: '#E5E5EA',
    marginBottom: 8,
  },
  card: {
    flexDirection: 'row',
    padding: 16,
    backgroundColor: '#FFFFFF',
    borderRadius: 12,
    marginBottom: 12,
  },
  cardContent: {
    flex: 1,
    marginLeft: 12,
    justifyContent: 'center',
  },
});
```

## Toast / Snackbar

### Toast Notification
```jsx
import { View, Text, StyleSheet, Dimensions } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withDelay,
  runOnJS,
} from 'react-native-reanimated';
import { useSafeAreaInsets } from 'react-native-safe-area-context';
import { useEffect } from 'react';

const { width: SCREEN_WIDTH } = Dimensions.get('window');

function Toast({ message, type = 'info', duration = 3000, onHide }) {
  const insets = useSafeAreaInsets();
  const translateY = useSharedValue(-100);

  useEffect(() => {
    // Show
    translateY.value = withSpring(0, { damping: 15 });

    // Hide after duration
    translateY.value = withDelay(
      duration,
      withSpring(-100, { damping: 15 }, () => {
        if (onHide) runOnJS(onHide)();
      })
    );
  }, []);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateY: translateY.value }],
  }));

  const colors = {
    info: '#007AFF',
    success: '#34C759',
    warning: '#FF9500',
    error: '#FF3B30',
  };

  return (
    <Animated.View
      style={[
        styles.toast,
        { top: insets.top + 8, backgroundColor: colors[type] },
        animatedStyle,
      ]}
      accessibilityRole="alert"
      accessibilityLiveRegion="polite"
    >
      <Text style={styles.toastText}>{message}</Text>
    </Animated.View>
  );
}

const styles = StyleSheet.create({
  toast: {
    position: 'absolute',
    left: 16,
    right: 16,
    paddingVertical: 14,
    paddingHorizontal: 20,
    borderRadius: 12,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.15,
    shadowRadius: 8,
    elevation: 8,
  },
  toastText: {
    color: '#FFFFFF',
    fontSize: 15,
    fontWeight: '500',
    textAlign: 'center',
  },
});
```
