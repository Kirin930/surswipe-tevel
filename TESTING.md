# Surwipe - Manual Testing Checklist

Complete this checklist before deploying to production.

## Test Environment Setup

- [ ] Test on real iOS device (iPhone)
- [ ] Test on real Android device
- [ ] Test on tablet (to verify mobile-only gate)
- [ ] Test on desktop browser
- [ ] Test with developer tools mobile emulation
- [ ] Verify webhook endpoint is configured and working

---

## 1. Desktop/Tablet Blocking

### Desktop Browser
- [ ] Open site on desktop browser (width >= 768px)
- [ ] Verify "Apri questo link da smartphone" screen appears
- [ ] Verify phone icon is visible
- [ ] Verify text is readable
- [ ] Verify gradient background displays correctly
- [ ] Verify app does NOT proceed to mobile interface

### Tablet
- [ ] Open on tablet device
- [ ] Verify blocking screen appears OR mobile interface (depending on screen size)
- [ ] Test in landscape orientation
- [ ] Test in portrait orientation

---

## 2. Mobile Interface - Intro Screen (Q1)

### Visual Elements
- [ ] App loads successfully on mobile
- [ ] Progress bar shows at 0%
- [ ] Question number "Domanda 1" is visible
- [ ] Question text "Sei interessato a lavorare in Tevel?" displays correctly
- [ ] Swipe tutorial with arrows is visible
- [ ] Card has gradient border and proper styling
- [ ] Animations play smoothly (fade in effects)

### Swipe Gestures - Right (YES)
- [ ] Swipe card to the right
- [ ] Blue "SI" overlay appears during swipe
- [ ] Card follows finger/pointer movement
- [ ] Card rotates slightly during drag
- [ ] Release before threshold (< 80px): card snaps back to center
- [ ] Release after threshold (> 80px): card slides off screen
- [ ] "SI" overlay scales and animates properly
- [ ] Smooth transition to form screen

### Swipe Gestures - Left (NO)
- [ ] Swipe card to the left
- [ ] Red "NO" overlay appears during swipe
- [ ] Card follows finger movement correctly
- [ ] Release before threshold: card returns to center
- [ ] Release after threshold: card slides off left side
- [ ] Screen resets back to intro screen
- [ ] State is reset (fresh start)
- [ ] Session ID is regenerated

### Edge Cases
- [ ] Try vertical scroll - should not trigger swipe
- [ ] Try diagonal swipe with more vertical movement - should ignore
- [ ] Quick tap - should not trigger swipe
- [ ] Multi-touch - should handle gracefully

---

## 3. Form Screen

### Visual Elements
- [ ] Progress bar shows at 20%
- [ ] "I tuoi dati" title is visible
- [ ] "Compila per continuare" subtitle shows
- [ ] Three input fields visible: Nome, Cognome, Email
- [ ] Labels are uppercase and styled correctly
- [ ] Swipe instruction at bottom is visible
- [ ] All animations play (staggered fade-ins)

### Form Validation - Nome (First Name)
- [ ] Try to swipe right with empty field - blocked
- [ ] Enter 1 character - shows error
- [ ] Enter 2+ characters - error disappears
- [ ] Field has focus styling (blue border)
- [ ] Error message appears below field

### Form Validation - Cognome (Last Name)
- [ ] Leave empty and swipe - blocked
- [ ] Enter 1 character - error shows
- [ ] Enter 2+ characters - valid
- [ ] Focus styling works

### Form Validation - Email
- [ ] Enter invalid email (no @) - error shows
- [ ] Enter email without domain - error shows
- [ ] Enter valid email - error disappears
- [ ] Email validation regex works correctly

### Invalid Swipe Attempt
- [ ] Fill only first name, swipe right
- [ ] Card shakes and returns to center
- [ ] Vibration triggers (if device supports)
- [ ] Error messages appear for invalid fields
- [ ] Red border shows on invalid inputs

### Valid Form Submission
- [ ] Fill all fields correctly
- [ ] Swipe right
- [ ] "AVANTI" overlay appears
- [ ] Card slides off screen
- [ ] Transitions to question screen
- [ ] Form data is saved to app state

---

## 4. Question Screens (Q2 onwards)

### Question 2
- [ ] Progress bar shows ~35%
- [ ] "Domanda 2" displays
- [ ] Question text shows correctly
- [ ] Swipe right records YES answer
- [ ] Swipe left records NO answer
- [ ] Smooth transition to next question

### Question 3
- [ ] Progress bar increases
- [ ] "Domanda 3" displays
- [ ] Different question text appears
- [ ] Swipe gestures work
- [ ] Answers are recorded

### Continue Through All Questions
- [ ] Progress bar increases with each question
- [ ] All configured questions appear
- [ ] Swipe right (YES) works for all
- [ ] Swipe left (NO) works for all
- [ ] Overlays appear correctly
- [ ] No lag or freezing
- [ ] Smooth animations throughout

### After Last Question
- [ ] Transitions to Captcha screen
- [ ] Progress bar shows 90%

---

## 5. Captcha Screen

### Visual Elements
- [ ] "Verifica di sicurezza" title shows
- [ ] Subtitle is visible
- [ ] reCAPTCHA widget loads
- [ ] reCAPTCHA is properly sized for mobile
- [ ] Submit button appears disabled (grayed out)
- [ ] Submit button shows "Invia risposte" text

### reCAPTCHA Interaction
- [ ] Click/tap reCAPTCHA checkbox
- [ ] Complete the challenge if presented
- [ ] On success, submit button becomes enabled
- [ ] Submit button changes color to gradient
- [ ] Submit button is now clickable

### Without Captcha
- [ ] Try to click submit while disabled - nothing happens
- [ ] Verify button cursor is "not-allowed"

---

## 6. Submission Process

### Successful Submission
- [ ] Click enabled submit button
- [ ] Transitions to "Invio in corso..." screen
- [ ] Spinner animation plays
- [ ] Progress bar shows 95%
- [ ] Wait for webhook response
- [ ] Check browser Network tab - POST request sent
- [ ] Verify payload structure is correct
- [ ] On success, transitions to success screen

### Success Screen
- [ ] Green checkmark icon appears
- [ ] "Grazie!" title shows
- [ ] "Risposte inviate correttamente" message visible
- [ ] Progress bar at 100%
- [ ] Animations play smoothly
- [ ] Screen stays visible (no auto-reset)

---

## 7. Error Handling

### Webhook Failure Simulation
- [ ] Disconnect internet before submitting
- [ ] OR configure invalid webhook URL
- [ ] Submit form
- [ ] Shows "Invio in corso..." screen
- [ ] After timeout, shows error screen
- [ ] Red error icon appears
- [ ] "Errore di invio" title shows
- [ ] Error message is readable
- [ ] "Riprova invio" button appears

### Retry Functionality
- [ ] Reconnect internet (or fix webhook)
- [ ] Click "Riprova invio" button
- [ ] Returns to submitting screen
- [ ] Attempts to send again
- [ ] On success, goes to success screen
- [ ] If fails again, shows error screen
- [ ] Maximum retries respected (2 attempts)

---

## 8. Data Validation

### Check Webhook Payload
Review the JSON payload sent to webhook:

- [ ] `version` field is "1.0"
- [ ] `timestamp_iso` is valid ISO timestamp
- [ ] `session_id` is a valid UUID v4
- [ ] `user.first_name` contains entered first name
- [ ] `user.last_name` contains entered last name
- [ ] `user.email` contains entered email
- [ ] `answers` array has all questions
- [ ] Each answer has `id`, `question`, and `answer` (boolean)
- [ ] `meta.user_agent` contains browser user agent
- [ ] `meta.language` contains browser language
- [ ] `meta.timezone` contains timezone
- [ ] `meta.screen` has width and height
- [ ] `meta.is_touch` is true on mobile
- [ ] `captcha.provider` is "recaptcha"
- [ ] `captcha.token` contains the reCAPTCHA token

### Answer Accuracy
- [ ] Q1 answer is always true (because form only shows after YES)
- [ ] Subsequent answers match your swipe choices
- [ ] Boolean values are correct (true for YES, false for NO)

---

## 9. User Experience & Performance

### Animations & Transitions
- [ ] All animations are smooth (60fps)
- [ ] No janky scrolling or movements
- [ ] Card swipe feels natural and responsive
- [ ] Overlays fade in/out smoothly
- [ ] Screen transitions are seamless
- [ ] Loading spinner rotates smoothly

### Touch Responsiveness
- [ ] Swipe response is immediate
- [ ] No delay between touch and card movement
- [ ] Multi-touch doesn't break the interface
- [ ] Accidental touches don't cause issues
- [ ] Touch targets are >= 44px (form inputs, buttons)

### Visual Polish
- [ ] Gradients render correctly
- [ ] Colors are vibrant and appealing
- [ ] Text is readable on all backgrounds
- [ ] Spacing and padding feel balanced
- [ ] Icons are crisp and properly sized

### Loading Time
- [ ] Initial page load < 2 seconds on 4G
- [ ] Fonts load without FOIT (flash of invisible text)
- [ ] No layout shift during load
- [ ] reCAPTCHA loads within 3 seconds

---

## 10. Cross-Browser Testing

### iOS Safari
- [ ] All features work
- [ ] Swipe gestures responsive
- [ ] Form inputs work properly
- [ ] No visual glitches
- [ ] Gradients render correctly

### Android Chrome
- [ ] All features work
- [ ] Touch events work
- [ ] Animations smooth
- [ ] No console errors

### Other Mobile Browsers (if applicable)
- [ ] Firefox Mobile
- [ ] Samsung Internet
- [ ] Edge Mobile

---

## 11. Accessibility

### Visual Accessibility
- [ ] Text contrast meets WCAG AA standards
- [ ] Font sizes are readable (16px minimum for body text)
- [ ] Touch targets are >= 44px × 44px
- [ ] Color is not the only indicator (text + color for YES/NO)

### Keyboard & Screen Reader (Limited on Mobile)
- [ ] Form inputs can be focused
- [ ] Tab order is logical
- [ ] Form labels are associated with inputs

---

## 12. Security & Privacy

### Data Handling
- [ ] No PII stored in localStorage
- [ ] No console.log with sensitive data in production
- [ ] Session ID is unique for each session
- [ ] reCAPTCHA token is included in payload

### Network Security
- [ ] Site is served over HTTPS (in production)
- [ ] Webhook endpoint uses HTTPS
- [ ] No sensitive data in URL parameters

---

## 13. Edge Cases & Stress Tests

### Rapid Interactions
- [ ] Quickly swipe multiple times - app handles gracefully
- [ ] Rapidly click submit button - only sends once
- [ ] Spam form inputs - no errors

### Unusual Data
- [ ] Enter very long name (100+ characters)
- [ ] Enter special characters in name (emoji, accents)
- [ ] Enter email with unusual but valid format
- [ ] Paste data into form fields

### Session Management
- [ ] Refresh page during survey - resets to intro
- [ ] Start survey, wait 5 minutes, continue - still works
- [ ] Multiple tabs/windows - each independent

### Network Conditions
- [ ] Slow 3G connection - app still usable
- [ ] Intermittent connection - shows appropriate errors
- [ ] Complete survey offline - shows error, retry works when back online

---

## 14. Production Readiness

### Configuration
- [ ] Webhook URL is set to production endpoint
- [ ] reCAPTCHA site key is production key
- [ ] Questions are final and reviewed
- [ ] All placeholder text is replaced

### Deployment
- [ ] Files uploaded to hosting platform
- [ ] HTTPS is enabled
- [ ] Custom domain configured (if applicable)
- [ ] Cache headers set appropriately

### Monitoring
- [ ] Webhook endpoint is monitored
- [ ] Error tracking configured (if applicable)
- [ ] Analytics configured (if applicable)

---

## Test Results Summary

Date tested: _______________
Tester name: _______________
Device tested: _______________
Browser: _______________

**Critical Issues Found:** _____
**Minor Issues Found:** _____
**Overall Status:** ☐ Pass  ☐ Fail  ☐ Needs Review

**Notes:**
_________________________________
_________________________________
_________________________________

---

## Sign-off

Developer: ________________  Date: ________
QA Tester: ________________  Date: ________
Product Owner: ____________  Date: ________
