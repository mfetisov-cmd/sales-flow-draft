# Vivid QR Code Screen Prototype

Interactive HTML prototype for the "Show QR code to client" screen with enhanced link sharing options.

## Features

- **QR Code Display**: Client can scan to open the application link
- **Copy Link**: Sales rep can copy link to clipboard
- **Send via Phone/Email**: Tab switcher to send link directly via SMS or email
  - Phone: Country code picker with EU markets (DE, ES, FR, IT, NL, LU, AT, CH)
  - Email: Simple email input
- **Create New Offer**: Start a new application flow

## Preview

Open `index.html` in any browser to view the interactive prototype.

## Usage

This prototype demonstrates the proposed UI changes to the sales-assisted onboarding flow for Vivid Business Accounts.

### Local Development

```bash
# Start a local server
python3 -m http.server 8080

# Open in browser
open http://localhost:8080
```

## Interactions

- Tab switcher animates between Phone and Email views
- Continue button enables when valid input is entered
- Success state shows spinner → green "Sent!" with toast notification
- All buttons have hover/click animations

---

*Vivid Money - Sales Onboarding Flow Prototype*
