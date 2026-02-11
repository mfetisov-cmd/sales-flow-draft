# SLS QR Screen Enhancement

## Context

Current final screen in sales-assisted flow only offers QR code or copy link. We're adding the ability for sales reps to send the application link directly via SMS or email.

**Prototype:** https://vivid-qr-screen.vercel.app/

**Why this flow:**
- Both factors (phone + email) must be confirmed before we can generate a no-auth application link
- Device binding happens in IFC on phone confirmation — client must confirm from their own device
- We cannot skip phone OTP entirely

**Resulting flows:**

1. **Phone:** Sales enters phone → SMS sent → Client clicks link → Pre-filled phone screen → OTP → Application
2. **Email:** Sales enters email → Email with magic link → Email auto-confirmed → Client enters phone → OTP → Application

**Client experience:** 1 SMS code + 1 email action (either input or link click).

## TODO

- [ ] Tab switcher: Phone / Email (below QR section)
- [ ] Phone tab: country picker (EU markets) + phone input
- [ ] Email tab: email input
- [ ] Send button with loading → success state + toast
- [ ] Send SMS/Email via NC
- [ ] Landing page with pre-filled phone number
- [ ] Email link click confirms email (magic link)
