# Mobile UI/UX Design Plugin for Claude Code

A comprehensive Claude Code plugin providing mobile UI/UX design best practices for React Native, iOS, and Android development.

## Installation

```bash
/plugin install geniusyinka/mobile-ui-ux-plugin
```

Or add via marketplace:

```bash
/plugin marketplace add olayinkaoshidipe/mobile-ui-ux-plugin
```

## What's Included

### Core Guidelines (`SKILL.md`)
- Touch-first design principles (44-48pt touch targets, thumb zones)
- Visual hierarchy and spacing (8pt grid system)
- Typography scales and line heights
- Navigation patterns (tab bars, stacks, modals, gestures)
- Common component guidelines
- Loading states and feedback
- Dark mode implementation
- Performance considerations

### iOS Human Interface Guidelines (`IOS-GUIDELINES.md`)
- SF Pro typography system
- iOS semantic colors
- Navigation bar and tab bar specs
- Button, list, and text field patterns
- Haptic feedback guidelines
- SF Symbols usage
- Safe area handling
- VoiceOver accessibility

### Material Design 3 Guidelines (`ANDROID-GUIDELINES.md`)
- Dynamic color / Material You theming
- M3 typography scale
- Shape and corner radius tokens
- Navigation components (bottom nav, rail, drawer)
- M3 button variants (filled, tonal, outlined)
- FAB, cards, chips, bottom sheets
- Motion and easing curves
- Elevation and surface tones
- TalkBack accessibility

### React Native Component Patterns (`COMPONENTS.md`)
Production-ready code examples using React Native Reanimated:
- Tab bar with animations
- Large title header with scroll collapse
- Swipeable list items
- Pull-to-refresh
- Gesture-driven bottom sheet
- Floating label text input
- Pressable button with haptics
- Action sheet modal
- Skeleton loaders
- Toast notifications

### Accessibility Guidelines (`ACCESSIBILITY.md`)
- Screen reader support (VoiceOver & TalkBack)
- Accessibility props and roles
- Color contrast requirements (WCAG)
- Dynamic Type / font scaling
- Reduce motion support
- Touch target sizing
- Focus management
- Form accessibility
- Testing checklist
- Common mistakes to avoid

## Auto-Triggering

This skill automatically activates when you discuss:
- Mobile app UI/UX
- React Native development
- iOS or Android design
- Touch interactions
- Mobile accessibility
- Bottom sheets, tab bars, modals
- Material Design or Human Interface Guidelines

## Manual Usage

Reference the skill explicitly in your prompts:

```
"Create a settings screen following mobile-ui-ux guidelines"
"Build an accessible form input using the mobile component patterns"
"Review this code for mobile accessibility issues"
```

## Requirements

- Claude Code v2.0.0+
- Works on macOS, Linux, and Windows

## File Structure

```
mobile-ui-ux-plugin/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json     # Marketplace metadata
├── skills/
│   └── mobile-ui-ux/
│       ├── SKILL.md              # Core guidelines
│       ├── IOS-GUIDELINES.md     # iOS HIG reference
│       ├── ANDROID-GUIDELINES.md # Material Design 3
│       ├── COMPONENTS.md         # React Native patterns
│       └── ACCESSIBILITY.md      # A11y checklist
└── README.md
```

## Contributing

Contributions welcome! Please open an issue or PR for:
- Additional component patterns
- Platform-specific updates
- Accessibility improvements
- Bug fixes

## License

MIT License - feel free to use, modify, and distribute.

## Resources

- [iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material Design 3](https://m3.material.io/)
- [React Native Accessibility](https://reactnative.dev/docs/accessibility)
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
