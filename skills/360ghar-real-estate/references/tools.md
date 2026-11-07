# 360Ghar MCP tool reference

Server: `https://api.360ghar.com/mcp`. "Guest" tools work without sign-in. All other tools need OAuth sign-in.

Dates: `YYYY-MM-DD` unless noted. Visit times: ISO 8601 with an offset, for example `2026-12-05T11:00:00+05:30`.

Pagination: list tools take `cursor` and `limit`. Pass the `next_cursor` value from one response as `cursor` in the next call. Some list tools take `include_total: true` to return a total count.

## System

| Tool | Auth | Parameters |
| --- | --- | --- |
| `user_system_status` | Guest | none |

## Discovery

| Tool | Auth | Parameters |
| --- | --- | --- |
| `discovery_search` | Guest | `query`, `city`, `locality`, `latitude`, `longitude`, `radius_km` (default 5, max 100), `property_type` (one or a list), `purpose`, `price_min`, `price_max`, `bedrooms_min`, `bedrooms_max`, `amenities` (names), `cursor`, `limit` (max 50), `include_total` |
| `discovery_property_get` | Guest | `property_id` (required) |
| `discovery_feed` | Guest | `latitude`, `longitude`, `purpose`, `limit` (default 10) |
| `discovery_amenities` | Guest | none |
| `discovery_swipe` | Auth | `property_id`, `is_liked` (both required) |
| `discovery_shortlist` | Auth | `cursor`, `limit` |
| `discovery_recommendations` | Auth | `latitude`, `longitude`, `limit` |

## Visits

| Tool | Auth | Parameters |
| --- | --- | --- |
| `visits_schedule` | Auth | `property_id`, `scheduled_date` (required, future, with offset), `notes` |
| `visits_list` | Auth | `status`, `cursor`, `limit` |
| `visits_get` | Auth | `visit_id` |
| `visits_cancel` | Auth | `visit_id`, `reason` |

## Bookings (short stay)

| Tool | Auth | Parameters |
| --- | --- | --- |
| `bookings_check_availability` | Guest | `property_id`, `check_in_date`, `check_out_date`, `guests` (default 1) |
| `bookings_get_pricing` | Guest | `property_id`, `check_in_date`, `check_out_date`, `guests` |
| `bookings_create` | Auth | `property_id`, `check_in_date`, `check_out_date`, `guests`, `special_requests` |
| `bookings_list` | Auth | `status`, `cursor`, `limit`, `include_total` |
| `bookings_get` | Auth | `booking_id` |
| `bookings_cancel` | Auth | `booking_id`, `reason` (both required) |

## Owner

| Tool | Auth | Parameters |
| --- | --- | --- |
| `owner_dashboard_overview` | Auth | none |
| `owner_properties_list` | Auth | `occupancy` (`occupied` or `vacant`), `q`, `cursor`, `limit`, `include_total` |
| `owner_properties_get` | Auth | `property_id` |
| `owner_properties_create` | Auth | Required: `title`, `property_type`, `purpose`, `full_address`, `city`, `locality`, `latitude`, `longitude`, `base_price`. Optional: `description`, `sub_locality`, `pincode`, `state`, `monthly_rent`, `daily_rate`, `security_deposit`, `maintenance_charges`, `area_sqft`, `bedrooms`, `bathrooms`, `balconies`, `parking_spaces`, `floor_number`, `total_floors`, `max_occupancy`, `minimum_stay_days`, `main_image_url`, `virtual_tour_url`, `amenity_ids` |
| `owner_properties_update` | Auth | `property_id` (required), `title`, `description`, `base_price`, `monthly_rent`, `daily_rate`, `is_available`, `max_occupancy`, `main_image_url` |
| `owner_properties_toggle_availability` | Auth | `property_id`, `is_available` |
| `owner_leases_list` | Auth | `property_id`, `status`, `cursor`, `limit` |
| `owner_leases_get` | Auth | `lease_id` |
| `owner_leases_terminate` | Auth | `lease_id`, `termination_date` (required), `reason` |
| `owner_rent_status` | Auth | `property_id`, `include_paid`, `cursor`, `limit` |
| `owner_rent_record_payment` | Auth | `rent_charge_id`, `amount`, `payment_date`, `payment_method` (required), `transaction_id`, `notes` |
| `owner_rent_history` | Auth | `property_id`, `lease_id`, `cursor`, `limit`, `include_total` |
| `owner_maintenance_list` | Auth | `property_id`, `status`, `priority`, `cursor`, `limit`, `include_total` |
| `owner_maintenance_update` | Auth | `request_id`, `status` (required), `scheduled_date`, `estimated_cost`, `actual_cost`, `resolution_notes` |

## Tenant

| Tool | Auth | Parameters |
| --- | --- | --- |
| `tenant_lease_current` | Auth | none |
| `tenant_rent_dues` | Auth | none |
| `tenant_rent_history` | Auth | `cursor`, `limit`, `include_total` |
| `tenant_maintenance_create` | Auth | `property_id`, `title`, `description`, `category` (required), `priority` (default `medium`) |
| `tenant_maintenance_list` | Auth | `status`, `cursor`, `limit`, `include_total` |

## Enum values

| Parameter | Values |
| --- | --- |
| `purpose` | `buy`, `rent`, `short_stay` |
| `property_type` | `house`, `apartment`, `builder_floor`, `room`, `villa`, `plot`, `condo`, `penthouse`, `studio`, `loft`, `pg`, `flatmate`, `office`, `shop`, `warehouse` |
| Visit `status` | `requested`, `confirmed`, `completed`, `cancelled`, `reschedule_suggested` |
| Booking `status` | `pending`, `confirmed`, `checked_in`, `checked_out`, `cancelled`, `completed` |
| Lease `status` | `draft`, `pending_signature`, `active`, `expiring_soon`, `expired`, `terminated`, `renewed` |
| Maintenance `status` | `open`, `in_progress`, `scheduled`, `completed`, `cancelled` |
| Maintenance `priority` | `low`, `medium`, `high`, `urgent`, `emergency` |
| Maintenance `category` | `plumbing`, `electrical`, `hvac`, `appliance`, `structural`, `pest_control`, `cleaning`, `other` |
| `payment_method` | `cash`, `bank_transfer`, `upi`, `cheque`, `online`, `other` |
