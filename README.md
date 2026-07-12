# Spire Maritime (spire-maritime)

Spire Maritime (Spire Global) delivers satellite and terrestrial AIS vessel data - global ship positions, static vessel characteristics, voyage and destination data, port events, and predicted ETAs. The flagship **Maritime 2.0** service is **GraphQL-first** at a single endpoint, with a separate **raw-TCP AIS stream** for real-time message delivery in NMEA 0183 format.

## Access model (read first)

- **Enterprise / contact-sales.** Spire Maritime is not a public self-signup API. Access is granted through a commercial agreement; there is no published per-request price list. Pricing in `plans/` and `finops/` is modeled as contact-sales and marked `reconciled: false`.
- **Token auth.** Both the GraphQL endpoint and the TCP stream authenticate with a Bearer token (`Authorization: Bearer <token>` for GraphQL; a token string sent on the TCP socket for the stream).
- **Transports.** Request/response data is **GraphQL over HTTPS** (`https://api.spire.com/graphql`). Real-time AIS is a **raw TCP socket** (`streamingv2.ais.spire.com:56784`) emitting NMEA 0183 sentences - **not** WebSocket and **not** SSE.
- **Migration note (important).** Following Spire Maritime's acquisition by **Kpler**, these APIs are being migrated/discontinued. `documentation.spire.com` now redirects to `servicedocs-sm.kpler.com`; the post-migration GraphQL endpoint is `https://api.sml.kpler.com/graphql` and the TCP stream moves to `streaming.sml.kpler.com:25000`. New integrations are directed to `developers.kpler.com` (migration support: `cs@kpler.com`). This entry records the Spire-branded surface as documented and flags the migration honestly.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/spire-maritime/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/spire-maritime/refs/heads/main/apis.yml)

## Tags

- Vessel Tracking
- AIS
- Maritime
- Satellite AIS
- Ship Tracking
- Real-Time Data
- Maritime Data
- Predicted ETA
- Port Events
- Location
- GraphQL

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Spire Maritime Vessels API

GraphQL `vessels` query returning up to 1,000 vessels per page - static data (name, MMSI, IMO, callsign, flag, shipType, dimensions), last position update (lat/long, course, heading, speed, `collectionType` SATELLITE or TERRESTRIAL), current voyage (destination, draught, ETA, matched port), and extended characteristics. Filter by MMSI/IMO/callsign/flag/shipType, area of interest (GeoJSON/WKT polygon), or last-position time range; cursor pagination via `first`/`after`.

- **Human URL:** [https://documentation.spire.com/maritime-2-0/](https://documentation.spire.com/maritime-2-0/)
- **Base URL:** `https://api.spire.com/graphql`

#### Properties

- [Documentation](https://documentation.spire.com/maritime-2-0/)
- [API Reference](https://documentation.spire.com/maritime-2-0/#graphql)
- [GraphQL](graphql/spire-maritime.graphql) — [GraphQL SDL](https://spec.graphql.org/)
- [Postman Collection](collections/spire-maritime.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/spire-maritime.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Spire Maritime Messages API

GraphQL query surface for decoded AIS messages (position and static/voyage reports) filtered by MMSI and time window, returned in large paginated batches. Used to pull recent and historical AIS message history over HTTPS rather than the always-on TCP stream. *Message-level field selection is representative and grounded in Spire's documented message model.*

- **Human URL:** [https://documentation.spire.com/maritime-2-0/](https://documentation.spire.com/maritime-2-0/)
- **Base URL:** `https://api.spire.com/graphql`

#### Properties

- [Documentation](https://documentation.spire.com/maritime-2-0/)
- [GraphQL](graphql/spire-maritime.graphql) — [GraphQL SDL](https://spec.graphql.org/)

### Spire Maritime Port Events API

GraphQL port-event queries - `portEventsByVessel`, `portEventsByLocation`, and `portEventsByShipType` - surfacing vessel arrivals and departures (ATA/ATD), port calls, and berth/anchorage events with state filtering.

- **Human URL:** [https://documentation.spire.com/maritime-2-0/#port-events](https://documentation.spire.com/maritime-2-0/#port-events)
- **Base URL:** `https://api.spire.com/graphql`

#### Properties

- [Documentation](https://documentation.spire.com/maritime-2-0/#port-events)
- [GraphQL](graphql/spire-maritime.graphql) — [GraphQL SDL](https://spec.graphql.org/)

### Spire Maritime Predicted ETA / Routing API

Predicted vessel routing and ETA - a `predictedVesselRoute` query taking an origin, destination, and vessel and returning the predicted sea route, distance, and estimated time of arrival / voyage duration. Complements the self-reported AIS ETA in `currentVoyage` with a computed, ocean-network ETA. *SDL shape is representative.*

- **Human URL:** [https://documentation.spire.com/routing-api/](https://documentation.spire.com/routing-api/)
- **Base URL:** `https://api.spire.com/graphql`

#### Properties

- [Documentation](https://documentation.spire.com/routing-api/)
- [GraphQL](graphql/spire-maritime.graphql) — [GraphQL SDL](https://spec.graphql.org/)

### Spire Maritime AIS TCP Stream

Always-on real-time AIS feed delivered over a **RAW TCP SOCKET** (not HTTP, not WebSocket). The client opens a TCP connection to `streamingv2.ais.spire.com` port `56784`, authenticates with a token, and receives encoded AIS sentences in NMEA 0183 v4.0 with a timestamp TAG-block prefix, one sentence per line. A server-side cursor resumes the stream after brief disconnects. **There is NO documented WebSocket (wss://) endpoint.**

- **Human URL:** [https://documentation.spire.com/tcp-stream-v2/using-the-tcp-stream/](https://documentation.spire.com/tcp-stream-v2/using-the-tcp-stream/)
- **Base URL:** `streamingv2.ais.spire.com:56784` (raw TCP)

#### Properties

- [Documentation](https://documentation.spire.com/tcp-stream-v2/using-the-tcp-stream/)

## Transport honesty

| Surface | Transport | Confirmed? |
|---|---|---|
| Vessels / Messages / Port Events / Predicted ETA | GraphQL over HTTPS (`api.spire.com/graphql`, Bearer) | Yes |
| Real-time AIS | Raw TCP socket (`streamingv2.ais.spire.com:56784`, NMEA 0183) | Yes |
| WebSocket (`wss://`) | — | **No such endpoint documented** |

See [review.yml](review.yml) for the full WebSocket determination and confirmed-vs-modeled breakdown.

## Common Properties

- [Domain Security](security/spire-maritime-domain-security.yml)
- [Authentication](authentication/spire-maritime-authentication.yml)
- [GitHub Organization](https://github.com/spireglobal)
- [LinkedIn](https://www.linkedin.com/company/spire-global)
- [Website](https://spire.com/maritime/)
- [Documentation](https://documentation.spire.com/)
- [Plans](plans/spire-maritime-plans-pricing.yml)
- [Rate Limits](rate-limits/spire-maritime-rate-limits.yml)
- [Fin Ops](finops/spire-maritime-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
