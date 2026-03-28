# EaseUI Email Templates

Production-grade HTML email templates for EaseUI, built to render consistently across all major email clients.

## Templates

| Template | File | Type | Mailgun Variables |
|---|---|---|---|
| **Private Launch** | `private-launch.html` | Marketing | `%recipient.first_name%`, `%mailing_list_unsubscribe_url%` |
| **Welcome** | `welcome.html` | Onboarding | `%recipient.first_name%`, `%mailing_list_unsubscribe_url%` |
| **Feature Update** | `feature-update.html` | Marketing | `%mailing_list_unsubscribe_url%` |
| **Password Reset** | `password-reset.html` | Transactional | `%recipient.reset_url%` |

## Design System (Email-Safe Tokens)

Adapted from EaseUI's web design system for email client compatibility.

### Color Palette

| Token | Light Mode | Dark Mode | Usage |
|---|---|---|---|
| Background | `#f4f4f5` | `#0f0f0f` | Email body wrapper |
| Card Surface | `#ffffff` | `#1a1a1a` | Main content card |
| Card Inset | `#fafafa` | `#111111` | Feature cards, step cards, security notes |
| Card Border | `#e4e4e7` | `#27272a` | Zinc-200 / Zinc-800 |
| Text Primary | `#18181b` | `#fafafa` | Headings, body text |
| Text Body | `#3f3f46` | `#d4d4d8` | Paragraphs |
| Text Secondary | `#71717a` | `#a1a1aa` | Metadata, labels |
| Text Muted | `#a1a1aa` | `#71717a` | Footer, timestamps |
| Accent Green | `#10B981` | `#10B981` | Buttons, links, arrows |
| Green Tint | `#ecfdf5` | `#052e16` | Badge backgrounds |
| Green Text | `#059669` | `#34d399` | Badge text |

### Typography

```
Font Stack: 'Inter', -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif
Mono Stack: 'SF Mono', 'Fira Code', 'Cascadia Code', 'Courier New', monospace

Heading (H1):  24px, weight 700, line-height 1.3, letter-spacing -0.02em
Heading (H2):  17-22px, weight 600, line-height 1.3-1.4, letter-spacing -0.02em
Body:          15px, weight 400, line-height 1.7
Secondary:     13-14px, weight 400, line-height 1.5-1.6
Label:         14px, weight 600, uppercase, letter-spacing 0.05em
Footer:        12px, weight 400, line-height 1.6
```

### Spacing (8pt Grid)

All spacing follows multiples of 8px (with 4px for micro-adjustments):
- `4px` — Micro gaps (label to subtitle)
- `8px` — Tight groups (within feedback list)
- `16px` — Label to content, step spacing
- `24px` — Card padding (inner), heading to body
- `32px` — Section separators
- `40px` — Major section gaps, card body outer padding
- `48px` — Card top padding

### CTA Buttons

Bulletproof button technique for Outlook compatibility:
- **VML `v:roundrect`** for MSO (Outlook Word engine)
- **Standard `<a>` tag** for all other clients
- Padding: `14px 40px`, border-radius: `8px`
- Background: `#10B981`, text: `#ffffff`, weight: 600

## Technical Architecture

### What Makes These Email-Safe

| Constraint | Solution |
|---|---|
| No CSS Grid/Flexbox | Table-based layouts with `<table role="presentation">` |
| Gmail strips `<style>` | All critical styles are inline; `<style>` used for progressive enhancement only |
| No CSS variables | Hard-coded color values in inline styles |
| No SVG icons | Unicode arrows (`→`, `•`) and text-based indicators |
| No custom fonts guaranteed | Inter with web-safe fallback stack |
| Outlook Word engine | VML `v:roundrect` for buttons, `<!--[if mso]>` conditionals |
| Dark mode inconsistency | `color-scheme` meta tags + `@media (prefers-color-scheme: dark)` |
| Image blocking | All images have descriptive `alt` text; email works without images |

### Dark Mode Support

Three layers of dark mode handling:

1. **Meta tags** — `<meta name="color-scheme" content="light dark">` tells Apple Mail to use dark mode
2. **CSS media query** — `@media (prefers-color-scheme: dark)` with `!important` overrides
3. **Outlook-specific** — `[data-ogsc]` and `[data-ogsb]` selectors for Outlook dark mode

### Responsive Design

- Max-width: `600px` (standard email pane)
- Mobile breakpoint: `640px`
- Fluid padding: Outer padding reduces from `40px` to `24px` on mobile
- Single-column layout: No column stacking required
- Touch targets: All CTA buttons are minimum 48px tall

## Sending with Mailgun

### Personalization Variables

Use Mailgun's recipient variables in the `recipient-variables` JSON:

```json
{
  "user@example.com": {
    "first_name": "Alex",
    "reset_url": "https://easeui.design/auth/reset?token=abc123"
  }
}
```

### Unsubscribe

Marketing emails use `%mailing_list_unsubscribe_url%` — Mailgun's built-in mailing list unsubscribe. This is auto-generated when sending via mailing lists.

For non-list sends, replace with your own unsubscribe URL.

### Transactional Emails

`password-reset.html` has no unsubscribe link (transactional emails are exempt per CAN-SPAM). It uses `%recipient.reset_url%` which must be provided per-recipient.

## Testing Checklist

Before sending any email campaign:

- [ ] Preview in browser at 600px width
- [ ] Preview in browser DevTools with `prefers-color-scheme: dark`
- [ ] Preview at 375px width (mobile simulation)
- [ ] Check all Mailgun variables are populated
- [ ] Verify logo loads from `https://easeui.design/images/Logo.png`
- [ ] Send test to Gmail (web + mobile)
- [ ] Send test to Apple Mail (macOS)
- [ ] Send test to Outlook 365
- [ ] Verify CTA button links are correct
- [ ] Verify unsubscribe link works
- [ ] Check text readability with images disabled

## Content Guidelines

When editing template content for new sends:

1. **Feature Update**: Replace the version badge, feature cards, and improvement list with actual release content. Keep the structure.
2. **Private Launch**: This is a one-time send — content is final as per `private-launch-email.md`.
3. **Welcome**: Review the 3-step onboarding flow and update if the product flow changes.
4. **Password Reset**: Content is static. Only the `reset_url` variable changes per user.

## File Structure

```
email/
├── private-launch-email.md     ← Copy spec (reference only)
├── private-launch.html         ← Private launch email template
├── welcome.html                ← Post-signup onboarding email
├── feature-update.html         ← Changelog/product update email
├── password-reset.html         ← Transactional password reset
└── README.md                   ← This file
```
