# Butterfly Experience — Codex handoff

Updated November 25, 2026. Start here when continuing the production portal, artist hosting, or event ticketing work.

## Current release and next action

Butterfly Experience version **64** was successfully published through OpenAI Sites. The approved source commit is `1ac756b5c5ddca85519ed4f571c14c677cfcad6f` in the Sites source repository. This GitHub handoff and the Sites source repository have independent histories. On the project workstation the release checkout is `.card-release/.sites-checkout` under the **Butterfly Experience** project. Other checkout folders were used for earlier releases and local previews. From another machine, open the existing Butterfly Experience project in Sites and obtain its current source checkout. Check the current live version before changing it. [Production site](https://butterfly-experience.com/) · [Artist Management](https://butterfly-experience.com/workspace#Artist%20Management)

**The portal release succeeded. The separate artist-hosting Worker deployment is still pending.** At the last check, the production Sites environment had no artist-domain Cloudflare settings. The command-line Cloudflare session pointed to a different organization. A Cloudflare sign-in handoff was opened in Tiffany’s browser. Check the actual account identity when resuming. Do not assume a browser tab or a previously saved CLI login grants access to Butterfly’s billing account.

1. Resume after sign-in to the intended Butterfly Cloudflare account. Identify an existing, dedicated artist-hosting zone and confirm it is active. If none is available, ask the owner which existing zone to use or how to proceed. The owner explicitly prohibited buying a domain or incurring charges during testing.
2. In the v64 source, follow `artist-host/README.md`. Configure the Cloudflare for SaaS fallback origin in the dedicated zone. The documented wildcard Worker route can intercept other applications if placed in a shared zone. Keep the existing Butterfly portal’s traffic intact.
3. Deploy `artist-host/wrangler.jsonc` in that account. Set the shared origin secret on both sides, the hosting credentials and zone settings on Sites, and a separately scoped registrar token on Sites. Use the provider secret stores. Confirm the account’s registrar prerequisites and billing ownership before enabling purchases.
4. Verify the public Worker against an authorized existing hostname and published artist page, including DNS, TLS, hostname-to-artist mapping, and the page proof. Record the actual result and deployment identifier. Use simulated orders for purchase tests. Keep live tickets, attendee check-ins, payments and email delivery out of setup tests.

## What Tiffany can manage

| Area | Product decision and behavior |
| --- | --- |
| Artist roster | Owner-only management of contacts with the Artist role. A managed artist has a private draft, preview, and explicitly published snapshot. Stage name, genre, location, bio, listen and booking links are public fields. EPK/rider and private contact details stay private. |
| Artist web addresses | The built-in published address is `/artists/<slug>`. The slug locks after first launch. Earlier releases tracked registrar details and supported URL forwarding. Version 64 adds direct hosting on the artist’s own hostname once the external Worker is operational. |
| Domain purchase | The artist is the registrant. Butterfly pays and manages the domain. Tiffany reviews the exact domain, first-year price, current renewal price, artist contact and auto-renewal choice, then confirms each purchase. Only supported one-year standard registrations use the integrated path. A changed price requires a new quote. |
| Existing domain | The artist can retain their registrar. The portal supplies DNS and validation records and tracks certificate issuance. The connection becomes Live after the portal verifies a signed response for the artist’s published page over HTTPS. For external apex domains, check DNS flattening or ALIAS support, or use a www hostname. |
| Order recovery | Registration state and quote consumption are durable. For an ambiguous purchase outcome, use **Check status** and reconcile the original order. Never issue a replacement purchase to diagnose a timeout. |
| Events | Full-window event workspace, guest list and check-in, ticket desk, accounting, production planning, and calendar. The Events grid shows the selected flyer at its original proportions. Administrators can confirm deletion from the grid or event overview. It removes the event from listings and the public calendar while retaining tickets and financial history. |
| Ticket fulfillment | The card is the ticket. One signed QR per admission with single-use check-in. Attendee profiles can open the assigned QR card, with the delivered attachment used when available. Preserve the same assignment on retries and resends. Rewards and perks are linked to attendee profiles. |

## Conversation index and decisions

The following are project-relevant summaries of the two Codex project conversations and five identifiable ChatGPT creative threads. Original chats and attachments remain in their respective workspaces.

- **Fix Stripe and ticket sales** — The Mad Hatter presale was designed around 300 slots, one admission per checkout and a $20 presale price. The project moved from a Stripe payment link to embedded checkout and tested ticket issuance, account sign-in, expiry of an unpaid hold, delivery, and scanning. A sandbox exercise covered purchase through check-in. Historical checkout results and prices are not a current inventory or payments report. This chat also contains the branded ticket deck, calendar, rewards, artist portal and Cloudflare work summarized here.
- **Improve event dashboard and accounting** — The organizer wanted one event to occupy the full workspace, task-specific navigation, an attendee list, and ticket revenue reflected in event accounting. Version 50 verified the live roster and revenue display. Later iterations designed a left rail for desktop/tablet, mobile shortcuts, separate guest-list/check-in and accounting views, and named Adobe Express social-flyer versions. Some navigation and marketing iterations were shown using a local preview. Inspect the released source before treating each preview as shipped.
- **Logo Design for Butterfly Experience** — Blue Morpho inspiration, a simple event-coordination logo, closer placement of the wordmark and butterfly, black text in one variant, and removal of swirls.
- **Butterfly Experience Logo Design** — A simple blue-and-purple Butterfly Experience mark for EDM event coordination and promotion. Use approved project artwork when modifying production pages.
- **Butterfly Sticker with QR Code** — A larger, readable QR placed within the open space without covering the butterfly, plus a holographic border. This was sticker design work. Admission QR codes are individual credentials.
- **Mad Hatter EDM Event** — Immersive walk-through concepts included a keyhole entrance, rabbit hole, table assignments and interactive tea trials. These were event concepts, not confirmed production bookings.
- **Mad Hatter EDM Party** — Venue-based party planning and four themed signature drink concepts. Treat menu and staging ideas as planning history unless the organizer confirms them.

## Event artwork, calendar and release history

- Versions **44–48** covered the public calendar flyer, original-size artwork, Adobe Express access from Marketing, upcoming events on Tickets, and owner calendar rescheduling. The reschedule workflow previews the change and its communications. In the historical check, the Mad Hatter event stayed on November 28 and no reschedule email was sent.
- **50** verified attendee and accounting views. **54** increased the mobile header logo. Local prototypes and saved reviews between releases are not evidence that every intermediate design reached production.
- **60–61** delivered the 11-character playing-card ticket deck, profile card access, Rave Rewards and perks, followed by the approved long-haired Alice. One hidden Mad Hatter slot among 300 is unique. The other ten designs repeat with balanced distribution. Each person still gets a unique admission code. The King of Hearts uses a painted portrait and a metal crown.
- Preserve the supplied gold “Mad Hatter” and crimson “Tea Party” lettering, lower text placement, prominent venue name, visible character portrait, and large matching rank and suit in opposite corners. Inspect both corners of every card and keep the QR readable. Put the two-line riddle at the bottom, with the answer managed separately. The organizer requested easier, editable answers and coded clues. Review individual full-size PNGs. The v61 local export is `outputs/mad-hatter-card-previews-v61.zip` in the project workspace.
- **62** introduced artist management as a saved release. **63** published it with event deletion and uncropped flyers. Live browser checks confirmed the owner roster and the Mad Hatter flyer’s original proportions. **64** published the domain-order and custom-hosting integration. Worker activation remains the open release item above.

## Implementation and verification map

Paths below refer to the Sites source checkout at v64.

| Task | Start here |
| --- | --- |
| Portal UI, artist publishing, authorization | `app/artist-management.tsx`, `app/artist-domains.tsx`, `app/artist-management-auth.ts`, `app/api/artist-management/route.ts` |
| Quotes, purchase confirmation, retry handling | `app/api/artist-domains/route.ts`, `lib/artist-registrar.ts`, migrations `0024_artist_domain_orders` and `0025_domain_quote_redemption` |
| Custom hostname, certificate, HTTPS verification | `lib/artist-hosting.ts`, `app/artist-hosting-probe.ts`, `app/api/artist-host/route.ts`, `artist-host/index.ts`, `artist-host/README.md` |
| Event list, flyers and deletion | `app/workspace-ui.tsx`, `app/api/os/route.ts`, `app/event-cover-manager.tsx`, `app/api/gallery/` |
| Attendees, revenue, rewards and tickets | `app/api/event-attendees/`, `app/api/event-finance/`, `app/api/rewards/`, `app/api/tickets/`, `app/api/stripe/` |
| Marketing, calendar, reschedule notifications | `app/api/marketing/`, `app/api/calendar/`, `tests/event-reschedule.cjs`, `tests/adobe-flyer-api.test.cjs` |

From the Sites checkout with its dependencies installed:

```sh
node_modules/.bin/tsc --noEmit
node --test tests/artist*.cjs
node_modules/.bin/wrangler deploy --dry-run --config artist-host/wrangler.jsonc
```

At v64, TypeScript, the production build, and all 13 artist tests passed. After publication, the Worker dry run and 11 focused domain tests passed. The live artist-host lookup returned HTTP 403 to an unauthenticated request. Separate local browser tests covered purchase confirmation and connecting an existing domain with simulated provider responses. A real artist hostname, production certificate, registrar purchase and production Worker deployment were outside those results.

## Working agreement

The owner retains authority over vendors, architecture, infrastructure commitments, and production releases. Cloudflare and the artist-owned, Butterfly-paid model were approved, as were publishing v64 and setting up the Worker. Individual real purchases require Tiffany’s confirmation of the domain and price. Use isolated fixtures for ticket and domain tests. Keep real QR payloads, guest lists, purchase records, private artist materials, credentials and billing details in their authorized systems. Record future releases with the version, source commit, verification performed and any remaining blocker.
