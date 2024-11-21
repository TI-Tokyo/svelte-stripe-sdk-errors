# Stripe.js SDK Integration Errors in SvelteKit

This is a reproduction repository for errors encountered whilst integrating the Stripe.js SDK with SvelteKit.

## Running the Project

1. Install dependencies:
```bash
npm install
# or
pnpm install
```

2. Start the development server:
```bash
npm run dev
# or
pnpm dev
```

3. Open your browser and navigate to:
```
http://localhost:5173
# or as shown in the terminal
```

## Known Issues

### Chrome Browser Connection Errors

The following errors appear in Chrome's console when attempting to connect to Stripe's services:

```javascript
POST https://r.stripe.com/b net::ERR_CONNECTION_REFUSED

r.stripe.com/b:1 Failed to load resource: net::ERR_CONNECTION_REFUSED

r.stripe.com/b:1 Failed to load resource: net::ERR_CONNECTION_REFUSED
```
