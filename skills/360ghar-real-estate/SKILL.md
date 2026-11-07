---
name: 360ghar-real-estate
description: Search, compare, and act on Indian property listings (buy, rent, short stay, PG, flatmates) through the 360Ghar MCP server. Use when the user wants to find a home, flat, PG, or office in India, see listing details, shortlist properties, schedule a site visit, book a short stay, or manage their own listings, leases, rent, or maintenance on 360Ghar.
---

# 360Ghar Real Estate

360Ghar is an Indian real estate platform. Its MCP server gives an agent tools to search listings, schedule visits, book stays, and run property management for owners and tenants.

- MCP server URL: `https://api.360ghar.com/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 with PKCE. The client opens a browser login (phone number and password). Search tools work without login.

## 1. Connect the MCP server

If the `360ghar` tools (for example `discovery_search`) are not available, add the server first.

Claude Code:

```bash
claude mcp add --transport http 360ghar https://api.360ghar.com/mcp
```

Cursor and other clients that use an `mcpServers` JSON file:

```json
{
  "mcpServers": {
    "360ghar": {
      "url": "https://api.360ghar.com/mcp"
    }
  }
}
```

Claude Desktop and claude.ai: Settings > Connectors > Add custom connector, URL `https://api.360ghar.com/mcp`.

The client discovers the OAuth server from `https://api.360ghar.com/.well-known/oauth-protected-resource/mcp` and registers itself. The user signs in once in the browser.

## 2. Start every session with a status check

Call `user_system_status`. It tells you if the user is signed in and which features are available. If the user is a guest, use only the guest tools (search, property details, feed, amenities). Ask the user to sign in before you call any other tool.

## 3. Workflows

### Find a property (guest)

1. If the user names amenities, call `discovery_amenities` to get the valid names.
2. Call `discovery_search`. Map the request to filters:
   - `purpose`: `buy`, `rent`, or `short_stay`
   - `property_type`: one type or a list (`apartment`, `builder_floor`, `villa`, `pg`, `flatmate`, and others; see `references/tools.md`)
   - `city`, `locality`, or `latitude` + `longitude` + `radius_km` (max 100)
   - `price_min`, `price_max` in INR; `bedrooms_min`, `bedrooms_max`
   - `query` for free text, for example "3 BHK near metro"
   - `limit` (max 50); pass `next_cursor` from the result as `cursor` for the next page
3. Call `discovery_property_get` with a `property_id` from the results for full details: price, deposit, area, floor, amenities, photos, and `virtual_tour_url` / `video_tour_url` when the listing has them. The owner phone number is masked for users who are not the owner.

For swipe-style browsing, call `discovery_feed`.

### Shortlist and recommendations (signed in)

1. `discovery_swipe` with `property_id` and `is_liked` (`true` = like, `false` = pass).
2. `discovery_shortlist` lists liked properties.
3. `discovery_recommendations` returns properties matched to the user's history.

### Schedule a site visit (signed in)

1. Confirm the property and the date and time with the user.
2. `visits_schedule` with `property_id` and `scheduled_date` in ISO 8601 with an offset, for example `2026-12-05T11:00:00+05:30`. The date must be in the future.
3. `visits_list`, `visits_get`, and `visits_cancel` manage existing visits.

### Book a short stay (signed in)

1. `bookings_check_availability` with `property_id`, `check_in_date`, `check_out_date` (`YYYY-MM-DD`), and `guests`.
2. `bookings_get_pricing` with the same values. Show the total to the user.
3. After the user confirms, call `bookings_create`.
4. `bookings_list`, `bookings_get`, and `bookings_cancel` (needs a `reason`) manage bookings.

### Owner: listings and property management (signed in)

- Listings: `owner_properties_list`, `owner_properties_get`, `owner_properties_create`, `owner_properties_update`, `owner_properties_toggle_availability`.
- Overview: `owner_dashboard_overview` (portfolio summary and analytics).
- Leases: `owner_leases_list`, `owner_leases_get`, `owner_leases_terminate`.
- Rent: `owner_rent_status` (dues), `owner_rent_record_payment`, `owner_rent_history`.
- Maintenance: `owner_maintenance_list`, `owner_maintenance_update`.

`owner_properties_create` needs `title`, `property_type`, `purpose`, `full_address`, `city`, `locality`, `latitude`, `longitude`, and `base_price`. Collect these from the user. Do not guess coordinates.

### Tenant (signed in)

- `tenant_lease_current`: the active lease.
- `tenant_rent_dues` and `tenant_rent_history`: rent due and paid.
- `tenant_maintenance_create` (needs `property_id`, `title`, `description`, `category`) and `tenant_maintenance_list`.

## 4. Rules

- Confirm with the user before every write: swipe, visit schedule or cancel, booking create or cancel, listing create or update, availability change, lease termination, rent payment record, maintenance create or update.
- Use only IDs that a tool returned. Never invent a `property_id`, `visit_id`, `booking_id`, `lease_id`, or `rent_charge_id`.
- All prices are in INR. Show them in Indian format, for example ₹45,000/month or ₹1.2 Cr.
- If a tool returns an auth-required error, tell the user to finish the 360Ghar sign-in in the browser, then retry.
- Show listing links only when the tool result includes them.
- Do not share owner or tenant personal data beyond what the tool result gives the signed-in user.

## Reference

Full parameter lists and enum values: [references/tools.md](references/tools.md).
